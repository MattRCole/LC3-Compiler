# LC3-Compiler
A Compiler for the LC3 assembly language. Compiles to both the Logisim LC3 2.0 and LC3 Simulate


The guts and gore of the LC3 to Logisim compiler:


-------------------------------------Data Structures---------------------------------------------------------------------------


Each line of a .asm file is considered to be a statement. Therefore, there is a corrisponding statement object.

Each statement is comprised of words. Therefore there is a corrisponding word object.

A word can be either a psudo op, operator, operand, number or label. There is a special case for the linker where any word starting with the @ symbol will be considered a linker instruction.

There are no child classes for the object word. It defines itself as any one of the 5 options or declairs itself to be void. (this is an exception case and should never happen.)

comments, linker instructions, the ',' character, and tab characters are considered to be "white space" and will be disregarded by the Statement object.

A statement may be empty, and consist of no words. If this is the case, the eos() member function will return true upon being called.

A program is a compilation of statements. A program may be empty.

If a program is steril, all empty statements have been removed, and all locations in memory have been stored in individual statements. A steril program is considered ready for the labeler. If a program contains instructions which are out of the currently allowed memory bounds or does not include both the .ORIG command and the .END command, it is considered incomplete, and cannot be sterilized.

A sterilized program is a program, but is not a child of the program object. This is due to C++ not allowing forward declaration of classes. Due to the possible confusion between a program and a steril program, the steril() member funcion can be called to determine the status of the current program object. False = non-steril. True = steril.


while the objects Program, Statement, and Word complete various tasks, they should themselves be seen as data structures and contain appropriately overloaded operators so to appear as such. The three objects in concert can be viewed as a 2 dimensional array as follows:

Word = Program[Statement_Index][Word_Index] 

The Program class will accept an fstream& object in its constructor and will populate an appropriate list of statements which, in turn, will each have an appropriate list of words. No deep error checking will take place, but various out of bounds errors will be thrown.

The Program class will return a sterilized Program object pointer meeting the requirements enumerated above. If the requirements are not met, appropriate exceptions will be thrown.

------------------------------------------------------------------Components of the Compiler-------------------------------------------------------------

The Labeler:

The Labeler requires a sterilized program. It will iterate through the program, determining label locations and references to labels. It will also check the range for the pc+offset of given instructions. The Labeler object will return a labeled program. Due to such little difference between a labeled program and a sterilized program, it will not be a child of the Program object, but rather a Program object with the boolean labeled variable set to true and the labeled() member function returning true.

Exceptions will be thrown apropriately reporting any user errors, to which the line number and cursor position will be given if applicable.

The Compiler:

The Compiler requires a labeld program. It will return a list of hexidecimal values representing instructions and data to be stored to memory. These values will be contained in a memory sized array of 65536 locations. These will not contain any instructions included in the linker. (Such as boot sequence and trap instructions.)

The Linker:

The Linker requires the memory array generated by the compiler. depending upon the linker instructions it will insert included librarys into the array in a hexadecimal format. Upon completion of this task, the linker will output the compressed contents of the memory array to an appropriately named output file with the header: "v2.0 raw\n"
