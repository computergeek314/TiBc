# TiBc
TiBc is a native compiler on the Ti89 for a B-like programming language currently in development. 

### To compile
To compile with GCC, simply use
```
make
````
To compile with any other compiler, my_compiler for instance, use
```
make CC=my_compiler
```
These output an executable `TiBc`

### To use
#### Unix
Use one of the following
```
./TiBc input_file.b
```
Or if you would like to specify an output file
```
./TiBc input_file.b output_file.out
```
#### Windows
Use one of the following
```
TiBc input_file.b
```
Or if you would like to specify an output file
```
TiBc input_file.b output_file.out
```

### The Language
The language TiBc compiles currently is an extremely simple and B-like.
#### Keywords
* `var`
  * Declares a variable or function. It cannot initialize a variable with a value.
  * Variables can only contain integer values of a predefined width - no other data type is supported or needed.
  * Example: `var my_var; var my_function(arg1, arg2){...`
* `if`
  * Gives a condition for the execution of a code block.
  * Example: `if(my_var == 2){my_var = my_var - 1;...}`
* `while`
  * Gives a condition for the repeated execution of a code block.
  * Example: `while(val > 1){output = output*val; val = val - 1}`
* `return`
  * Specifies the output of a function
  * Example: `var my_function(a, b){var output; ... return output;}`
#### Currently Supported Operators
* Arithmetic operators:```+ - * /```
* Logical operators:```& | !```
* Comparison operators:```< > == !=```
* Referencing operators:```* &```
* Bitwise operators:```| & ~```

Note that ```|``` and ```&``` act as both boolean and bitwise OR and AND respectively right now. Short circuiting boolean operators have not been implemented yet.

The language follows standard C order of operations.
#### Example Code
A few examples are in the `examples` folder

*factorial*
```
var factorial(n){
  if(n < 2){
    return 1;
  }
  
  return n*factorial(n - 1);
}
```

### Output
TiBc currently outputs Motorola 68k assembly which can be assembled with Easy68k. With slight modification of the main routine, there is also support for outputting pseudo-assembly which follows the rules below. Note that the 68k assembly is directly translated from the pseudo-assembly, and all optimization occurs on the pseudo-assembly and not the actual output. This can lead to small inefficiencies, but makes the compiler portable to other architectures with minimal effort.

#### pseudo-assembly information

Places on the stack are referenced relative to the current location of the stack pointer. Positive references are where the stack is going, and negative references on the stack refer to previous values pushed onto the stack. 

Stack relative references are with respect to the width of the architectures native word (or possibly a different width). On the Ti89, each integer is 2 bytes, so `SSP -1` would actually decrement the stack pointer by 2 bytes, not one.

*pseudo-assembly opcodes*
* PUSH
  * Pushes an individual value onto the stack, either a constant or relative to the stack pointer.
* POP
  * Pops an individual value from the top of thte stack, storing it into either a global address, indirect address on the stack, or into a value on the stack
* MOV
  * Moves a value between stack or global addresses.
* BZSTACK/BZOP
  * Pops a value from the stack and branches to the address given if that value was 0
  * Branches to the address given if the value in the given register was 0
* BNZSTACK/BNZOP
  * Pops a value from the stack and branches to the address given if that value was not 0
  * Branches to the address given if the value in the given register was not 0
* SSP
  * Changes the stack pointer directly by n addresses. If positive, make the stack bigger. If negative, make it smaller by that number.
* ADDSTACK/ADDOP
  * Pops the last two values on the stack and pushes their sum.
  * Adds the values in the two given registers and stores it in the second register.
* SUBSTACK/SUBOP
  * Pops the last two values on the stack and pushes the second subtracted from the first.
  * Subtracts the value in the second register given from the value in the first register given and stores it in the second register.
* MULSTACK/MULOP
  * Pops the last two values on the stack and pushes their product.
  * Multiplies the value in the first register given with the value in the second register given and stores it in the second register.
* DIVSTACK/DIVOP
  * Pops the last two values on the stack and pushes the first divided by the second.
  * Divides the value in the first register given by the value in the second register given and stores it in the second register.
* ORSTACK/OROP
  * Pops the last two values on the stack and pushes the result of a bitwise or between the two.
  * Performs bitwise OR between the values in the two given registers and stores the result in the second register.
* ANDSTACK/ANDOP
  * Pops the last two values on the stack and pushes the result of a bitwise and between the two.
  * Performs bitwise AND between the values in the two given registers and stores the restlt in the second register.
* NOTSTACK/NOTOP
  * Pops the last value on the stack and pushes the result of a logical not of the value.
  * Replaces the value in the given register with its logical complement.
* LTSTACK/LTOP
  * Pops the last two values on the stack and pushes `1` if the first was less than the second, otherwise it pushes `0`
  * Tests if the value in the first register given is less than the value in the second register given and stores the logical result in the second register.
* GTSTACK/GTOP
  * Pops the last two values on the stack and pushes `1` if the first was greater than the second, otherwise it pushes `0`
  * Tests if the value in the first register given is greather than the value in the second register given and stores the logical result in the second register. 
* EQSTACK/EQOP
  * Pops the last two values on the stack and pushes `1` if the first was equal to the second, otherwise it pushes `0`
  * Tests if the value in the first register given equals the value in the second register given and stores the logical result in the second register.
* NEQSTACK/NEQOP
  * Pops the last two values on the stack and pushes `0` if the first was equal to the second, otherwise it pushes `1`
  * Tests in the value in the first register given does not equal the value in the second register given and stores the logical result in the second register.
* JMPSTACK/JMP
  * Pops the last value on the stack and jumps th that address.
  * Jumps to the address stored in the given register.
  * Program labels, memory addresses, and integers are all expected to have the same bit-width.
* DEREFSTACK
  * Pops the last value on the stack and pushes the value in memory stored at the address popped.
  * Replaces a register's value with the value stored at that address.
* CONSTANT
  * Defines an in-place constant in memory

### To Do
* Simple optimization
* Allocate variables to registers
* Output \_nostub executables which run on Ti89 and Ti92 calculators
* Get the compiler to run on the calculator itself with a text editor to make it a native compiler.

Special thanks to Bisqwit for showing that making a compiler isn't impossible at all.
