# Table of Contents

1. [Writing Patterns](#toc_1)
2. [Supported Language Features](#toc_2)
3. [Language limitations](#toc_3)
4. [Variables](#toc_4)
5. [Constants](#toc_5)
6. [Functions](#toc_6)
7. [Expansion Board](#toc_7)

# Writing Patterns

Enter code above, and everything you type is compiled on the fly. If your program is valid, it is sent down to Pixelblaze, and you'll see your changes instantly! Look for syntax or run-time errors in the left sidebar and the status area just below the editor.

Pixelblaze looks for a few exported functions that it can call to generate pixels.

The exported `render(index)` function is called for each pixel in the strip. The `index` argument tells you which pixel is being rendered. Use the `hsv` or `rgb` function to set the current pixel's color.

The exported `beforeRender(delta)` function is called before it is going to render a new frame of pixels to the strip. The `delta` argument is the number of elapsed milliseconds (with a resolution of 6.25ns!) since the last time `beforeRender` was called. You can use `delta` to create animations that run at the same speed regardless of the frame rate.

Pixelblaze's language is based off of JavaScript (ES6) syntax, but with a subset of the language features available. All numbers in Pixelblaze are a 16.16 fixed-point numbers. This can handle values between -32,768 to +32,768 with fractional accuracy down to 1/65,536ths.

A global called `pixelCount` is defined based on how many pixels you've configured in settings. You can use this in initialization code or in any function.


# Supported Language Features

* All of the usual math operators work. Most work on 16.16 fixed-point math. The bit-wise operators work on the top 16 bits. `=`, `+`, `-`, `!`, `*`, `/`, `%`, `>>`, `<<`, `~`, `^`, `>`, `<`, `>=`, `<=`, `==`, `!=`, `||`, `&&`, `?`
* Logical operators work like JavaScript and carry over the value, not just a boolean. e.g. `v = 0 || 42` will result in 42.
* Trig and other math functions. `abs`, `floor`, `ceil`, `min`, `max`, `clamp`, `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `atan2`, `sqrt`, `exp`, `log`, `log2`, `pow`, `random`
* Declare global or function-local variables using `var` or globals implicitly.
* Use `if` and `else` to have some code run conditionally.
* Use `while` and `for` for making loops and use `break` and `continue` statements to escape a loop or loop early.
* Define functions using the `function` keyword or short lambda-style form.
  * `function myFunction(arg1) {return arg1 * 2}`
  * `myFunction = (arg1) => arg1 * 2`
* Functions can be stored in variables, passed as arguments, and returned from other functions.
* Create arrays using the `array(size)` function and access them with the bracket syntax. Arrays can be passed around just like any other type.

# Language limitations

This are language features you'd expect to work writing JavaScript that won't run on Pixelblaze.

* Objects, named properties, classes, etc.
* Garbage collection or freeing memory. Arrays are currently the only dynamically allocated memory, and you can't (yet) free them once created.
* Closures aren't supported, so any function defined in another function won't have access to parameters or local variables. It can still access globals or its own parameters.
* `switch` + `case` statements. You can use chained `else if` statements, or put functions in an array and use it as a lookup table.

```
modes[0] = () => {/* do mode 0 */};
modes[1] = () => {/* do mode 1 */};
// ...
modes[currentMode]();
```

* Narrow scoped variables using `let` or read-only variables with `const`
* Array literals

# Variables

The `pixelCount` variable is available as a global even during initialization. This is the number of LED pixels that have been configured in settings.

Variables can be created/assigned implicitly with the `=` operator. e.g.: `foo = wave(time(0.1))` or explicitly using the `var` keyword. e.g.: `var foo = 1`.

You can also declare local variables inside functions using the `var` keyword.


# Constants

`E` , `PI`, `PI2`, `PI3_4`, `PISQ`, `LN2`, `LN10`, `LOG2E`, `LOG10E`, `SQRT1_2`, `SQRT2`

# Functions

### Math Functions

#### abs(`v`)
#### acos(`x`)
#### asin(`x`)
#### atan(`x`)
#### atan2(`y`, `x`)
#### ceil(`v`)
#### clamp(`value`, `low`, `hi`)
Clamps `value` such that it isn't less than `low` or greater than `high`
#### cos(`angleRads`)
#### exp(`v`)
#### floor(`v`)
#### log(`v`)
#### log2(`v`)
#### max(`v1`, `v2`)
#### min(`v1`, `v2`)
#### pow(`base`, `exponent`)
#### random(`max`)
A random number between 0.0 and `max` (exclusive)
#### sin(`angleRads`)
#### sqrt(`angleRads`)
#### tan(`angleRads`)

### Waveform Functions

#### time(`interval`)

A sawtooth waveform between 0.0 and 1.0 that loops about every 65.536*`interval` seconds. e.g. use .015 for an approximately 1 second.
#### wave(`v`)

Converts a sawtooth waveform `v` between 0.0 and 1.0 to a sinusoidal waveform between 0.0 to 1.0. Same as `(1+sin(v*PI2))/2` but faster. `v` "wraps" between 0.0 and 1.0.
#### square(`v`, `duty`)
Converts a sawtooth waveform `v` to a square wave using the provided `duty` cycle where `duty` is a number between 0.0 and 1.0. `v` "wraps" between 0.0 and 1.0.
#### triangle(`v`)
Converts a sawtooth waveform `v` between 0.0 and 1.0 to a triangle waveform between 0.0 to 1.0. `v` "wraps" between 0.0 and 1.0.

### Pixel / Color Functions

#### hsv(`hue`, `saturation`, `value`)

Sets the current pixel by calculating the RGB values based on the HSV color space. `Hue` "wraps" between 0.0 and 1.0. Negative values wrap backwards. For LEDs that support it, this uses 24-bit color plus an additional 5 bits of brightness control giving a high dynamic range especially at lower light levels and reduces posterization.

#### hsv24(`hue`, `saturation`, `value`)

Sets the current pixel by calculating the RGB values based on the HSV color space. `Hue` "wraps" between 0.0 and 1.0. Negative values wrap backwards. This uses 24-bit color only, even if the LEDs support additional resolution and may reduce flickering in some LEDs.

#### rgb(`red`, `green`, `blue`)

Sets the current pixel to the RGB value provided. Values range between 0.0 and 1.0.

### Input / Output Functions

**NOTE** that GP2 is used for NeoPixel and WS2811/12/13 support and can't be used unless the trace on the bottom is cut.

#### analogRead(`pin`)
Reads the value from the pin as a number between 0.0 and 1.0.

#### pinMode(`pin`,`mode`)
Set the pin mode as an `INPUT`, `INPUT_PULLUP`, `INPUT_PULLDOWN`, `OUTPUT`, `OUTPUT_OPEN_DRAIN`, or `ANALOG`.

`INPUT_PULLUP` works for GP2*, GP4, GP5, and GP12 but not for GP16. Useful for buttons, connect between the pin and GND. Pins will read `LOW` or zero while the button is pressed.

`INPUT_PULLDOWN_16` works only for GP16. Can be used with a button, connect between GP16 and 3.3v. Pins will read `HIGH` or 1.0 while the button is pressed.

#### digitalWrite(`pin`,`state`)
Set a pin `HIGH` or `LOW`. Any non-zero value will set the pin `HIGH`.

#### digitalRead(`pin`)
Read a pin state, returns 1.0 if the pin is `HIGH`, 0.0 for `LOW` otherwise.

#### touchRead(`pin`)
Detect touch and proximity on a pin using capacitive sensing techniques. Returns a value between 0.0 and 1.0 depending on how much capacitance is detected on the pin. You can also specify one of these constants: T0, T2, T4, T6, T7.

### Clock / Time Functions

When the discovery service is enabled and Pixelblaze is connected to the internet, it will know what time it is and these functions can be used.

#### clockYear()
#### clockMonth()
#### clockDay()
#### clockHour()
24 hour format. `13` = 1pm.
#### clockMinute()
#### clockSecond()
#### clockWeekday()
Sunday = 1, Monday = 2, etc.

# Sensor Expansion Board

Pixelblaze supports a sensor expansion board that adds:

* A microphone and signal processing that gives:
	* `frequencyData` - 32 element array with frequency magnitude data ranging from 12.5-10khz
	* `energyAverage` - total audio volume
	* `maxFrequency` and `maxFrequencyMagnitude` - detects the strongest tones with resolution of about 39Hz
* A 3-axis 16G `accelerometer` - 3 element array with [x,y,z]
* An ambient light sensor - `light` can be used to automatically dim displays for nightlights
* 5 `analogInputs` - 5 element array with analog values from A0-A4

Each of these can be accessed in a pattern by using the `export var` syntax, with optional defaults if the board is not connected.

```
export var frequencyData
export var energyAverage
export var maxFrequencyMagnitude
export var maxFrequency
export var accelerometer
export var light
export var analogInputs

```