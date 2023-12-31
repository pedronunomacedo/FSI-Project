# WEEK 7

## SEEDs LAB

Link: https://seedsecuritylabs.org/Labs_20.04/Software/Format_String/

### Task 1 - Crashing the Program
- The instruction "nc 10.9.0.5 9090" compiles the given server that has de format string vulnerability.
- Adding the command "echo hello" will send the message "hello" to the server started. Doing this, we will see this message printed out by the server.

![Task1 - result](img/category-software/Format_String/Task1.png)

### Task2 - Printing Out the Server Program's Memory
#### Task2.A - Stack Data
- The purpose of this task is to print data of the stack. And the goal is to know how many %x format specifiers we need to use in order to print out our 4 bytes. Doing this, we will know when we get to the address where our buffer is in memory.

- As we can see below, the value after is 0x11223344.

![Task2.A - result](img/category-software/Format_String/Task2(code-build_string_py_file).png)

#### Task2.B - Heap Data
- The purpose of this task is to print out the secret message stored in the heap area.

![Task2.B - result](img/category-software/Format_String/Task2B(result).png)

- In the picture below, we can see the file used in the server (in our case, is called "badFile"). (line 17) 

- In fact, we tried to figured out the number of %x we would need to get our hexadecimal mumber to be printed on the screen, caming to the conclusion that we would need 65 %s format specifiers in order to print our hexadecimal number. We used "%x"*63 in order to store 63 times the format specifier %x, and, at the end, we are concatenating our hexadecimal number. We have, in total, 65 bytes because we store 1 byte (8 bits) of our number encoded in the content array, then we store 63 %x format specifiers and then 1 byte for our hexadecimal number (1 + 63 + 1 = 65 bytes).

![Task2.B - code](img/category-software/Format_String/Task2B(code).png)

### Task3 - Modifying the Server Program's Memory
#### Task3.A - Change the value to a different value
- In this case, we are supposed to change the content of the target variable (to something else we want). We chose to print the number f bytes between the address of the printf and the head address of our buffer. 
- Firstly, we change the number variable to the address of the target variable (in this case, is 0x080e5068).
- Secondly, we build a string that contains %x 63 times and, at the end, the number of bytes read until there (using the format specifier %n). At the end, we add this string to the content array. 
- Doing this, we will change the value stored in the target variable's address (0x000000f0, in our case).

![Task3.A - result](img/category-software/Format_String/task3A(result).png)
![Task3.A - code](img/category-software/Format_String/task3A(code).png)

#### Task3.B
- In this task, we want to change the content of the target variable's address to 0x5000. To do so, we though of trying to print 20480 (integer number of the hexadecimal 0x5000) times the %x format specifier. Doing so, we can use %n to print this number, as this format specifier refers to the number of bytes read until that moment.

![Task3.A - result](img/category-software/Format_String/task3B(result).png)

- In our code, you can see we calculated how much we needed to get 0x5000 as the %n output, and we got 20248. This printed value counts as an integer number, so we need to take one %x to compensate the extra character to be printed (0x5000).

![Task3.B - code](img/category-software/Format_String/task3B(code).png)


