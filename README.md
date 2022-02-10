# QMK SOCD Cleaner
This is a basic QMK SOCD (Simultaneous Opposing Cardinal Directions) Cleaner code written in QMK (C Programming Language). This code is compatible with any keyboard that uses the QMK firmware.

# Usage
1. Copy and paste the code into your keymap.c file. 
2. Map GC_UP, GC_LEFT, GC_DOWN, and GC_RIGHT to WASD on a new layer in keymap.c
3. Compile your firmware using QMK MSYS. 
4. Flash your firmware onto your keyboard using QMK Toolbox.

For more information on compiling and flashing QMK Firmware refer to the [QMK Documentation](https://docs.qmk.fm/#/newbs).

**Note:** If you use ESDF as I do, replace KC_W, KC_A, KC_S, an KC_D with KC_E, KC_S, KC_D, and KC_F respectively in the code.

# Disclaimer
Due to the nature of QMK and the ability to program your keyboard. This is NOT tournament legal.

# Stickless / Hitbox SOCD Cleaner
This SOCD Cleaner treats **Left + Right = Neutral (no input)** and **Up + Down = Up**. This is the default behavior on Stickless/Hitbox arcade controllers.

Place the following code before keymap section of your keymap.c file
```
enum custom_keycodes {      
	GC_UP = SAFE_RANGE,
	GC_LEFT,
	GC_DOWN,
	GC_RIGHT
};
```

Place the following code after the keymap section of your keymap.c file.
```
bool pressed_up = false;
bool pressed_down = false;

bool pressed_left = false;
bool pressed_right = false;

bool process_record_user(uint16_t keycode, keyrecord_t *record) {
	switch (keycode) {
		case GC_UP:
			if (record->event.pressed) {
				pressed_up = true;
				register_code(KC_W);
				if (pressed_down) {
					unregister_code(KC_S);
				}
			} else {
				pressed_up = false;
				unregister_code(KC_W);
				if (pressed_down) {
					register_code(KC_S);
				}
			}
			return false;
		case GC_DOWN:
			if (record->event.pressed) {
				pressed_down = true;
				if (pressed_up) {
					register_code(KC_NO);
				} else {
					register_code(KC_S);
				}
			} else {
				pressed_down = false;
				unregister_code(KC_S);
			}
			return false;
		case GC_LEFT:
			if (record->event.pressed) {
				pressed_left = true;
				if (pressed_right) {
					unregister_code(KC_D);
				} else {
					register_code(KC_A);
				}
			} else {
				pressed_left = false;
				unregister_code(KC_A);
				if (pressed_right) {
					register_code(KC_D);
				}
			}
			return false;
		case GC_RIGHT:
			if (record->event.pressed) {
				pressed_right = true;
				if (pressed_left) {
					unregister_code(KC_A);
				} else {
					register_code(KC_D);
				}
			} else {
				pressed_right = false;
				unregister_code(KC_D);
				if (pressed_left) {
					register_code(KC_A);
				}
			}
			return false;
		default:
			return true;
	}
};
``` 
# Further Reading

[QMK Documentation](https://docs.qmk.fm/#/)
[Hitbox: What is SOCD](https://www.hitboxarcade.com/blogs/support/what-is-socd)
