# dynamicrune
dynamic Rune documentation

> **Warning** Rune scripting sent over a party may be malicious, only join another party if you trust the participants!

## Functions supplied by dynamic
- `dynamic::log(string)` -> `()`
   - Arg 0: The message to log.
   - Returns: Nothing.
   - Description: Logs a message to the terminal.
- `CVar::get_output(string, string)` -> `String`
   - Arg 0: PXScript to execute, such as `global/PlayerName.GetDataString()`
   - Arg 1: Type of the content you are trying to get the output for, case-sensitive. Such as: `Int`, `String`, etc.
   - Returns: String content of the variable.
   - Description: Attempts to get a game variable's value into your Rune script, which you may then use as you wish.
- `parse::(number_type)(string)` -> `number_type`
   - Arg 0: The string you want to be parsed as the specified type.
   - Returns: The number type you specified.
   - Description: Attempts to parse the input String as a specified number type, such as `i32`, `u8` or `f32`.
- `PXScript::execute(string, bool)` -> `()`
   - Arg 0: The PXScript you want to execute.
   - Arg 1: Should this script be sent to the connected party? This is usually unwanted unless you're creating advanced scripts.
   - Returns: Nothing.
   - Description: Executes the given script and optionally sends it to the active party.
- `dynamic::create_thread_key(string)` -> `()`
   - Arg 0: The unique thread keys name.
   - Returns: Nothing.
   - Description: Creates a new global thread key with the value of `true`.
- `dynamic::set_thread_key_value(string, bool)` -> `()`
   - Arg 0: The unique thread keys name.
   - Arg 1: The new value to assign it, true or false.
   - Returns: Nothing.
   - Description: Changes the value of an existing thread key. If it doesn't exist, it is then created and assigned the value from Arg 1.
- `dynamic::get_thread_key(string)` -> `bool`
   - Arg 0: The unique thread keys name.
   - Returns: The value of that thread key.
   - Description: Gets the value of the specified thread key.
- `dynamic::sleep_secs(u64)` -> `()`
   - Arg 0: How long (in seconds) the thread should be put to sleep for.
   - Returns: Nothing.
   - Description: Puts the current script thread to sleep for *x* amount of seconds. All scripts run on their own thread.
- `dynamic::sleep_ms(u64)` -> `()`
   - Arg 0: How long (in milliseconds) the thread should be put to sleep for.
   - Returns: Nothing.
   - Description: Puts the current script thread to sleep for *x* amount of milliseconds. All scripts run on their own thread.

## Custom Keywords
- `import (file).rn`
   - Usage Example:
   ```rust
   import something.rn
   
   pub fn main() {
       // "import" has to be at the start of your script.
       // The imported files functions are now available for use in this script.
   }
   ```
   - Description: Attempts to import the given file (must be at the same path as the DLL) and append its content to the very end of your current script internally.

## Usage Examples
1. Adding force to the currently mounted horse
```rust
pub fn main() {
    // Add force to X, Y and Z.
    PXScript::execute("global/Horse.AddForce(1, 0, 1);", false);
    
    // Or as a custom function:
    add_force(1, 0, 1);
}
 
fn add_force(x, y, z) {
    // Use `` over "" for when you want to embed variables into a string.
    PXScript::execute(`global/Horse.AddForce(${x}, ${y}, ${z});`, false);
}
```

2. Defining variables and using them
```rust
pub fn main() {
    // A standard, basic variable that can have its value changed.
    let my_string = "Hello World";
    // Change the value to "Bye World"
    my_string = "Bye World";
    
    // Constant variables which value cannot be changed.
    const MY_CONSTANT_STRING = "Hello World";
    // Try and change the value of the string.
    MY_CONSTANT_STRING = "Bye World"; // Compile error!
}
```

3. Execute a script every second
```rust
pub fn main() {
    // Create a thread key so that we can stop the while-loop whenever we want in a new script.
    dynamic::create_thread_key("Example");
    
    // Start the loop as long as the value of the "Example" thread key is true.
    while dynamic::get_thread_key("Example") {
          PXScript::execute("global/Horse.AddForce(1, 0, 1);", false);
    }
}
```

4. Stop a looping script
```rust
pub fn main() {
    // Replace "Example" with the name of your thread key.
    dynamic::set_thread_key_value("Example", false);
    // The loop should now stop after compiling.
}
```
