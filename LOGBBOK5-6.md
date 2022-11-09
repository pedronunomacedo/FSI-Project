# WEEK 5

## SEEDs LAB

Link: https://seedsecuritylabs.org/Labs_20.04/Software/Buffer_Overflow_Setuid/

### Task 1
#### Getting Familiar with Shellcode


- When we run the code, we can get to the root shell with both the 32bit and 64bit (a32.out and a64.out), because our system is 64bit, but it also has compatibility with 32bit commands.
- What the code does is copy the shellcode (string of the code that calls the shell) to the stack..
- Then we forced the code to be an int and were able to call the codeshell using func(). So, with this we are able to have access to root shell.


![Terminal print of task 1](img/category-software/Logbook\ 5-6/Task1.png)

### Task2
#### Understanding the Vulnerable Program

- This task was a preparation for task 3. We first had to turn off some protections linux has by default. Then we had to give ownership to stack.c to root and give the current user of calling the program with root permissions.
- For task 3 we needed stack L1, so we checked that it had a buffer of size 517.

![Terminal print of task 2](img/category-software/Logbook\ 5-6/Task2.png)

### Task3
#### Launching Attack on 32-bit Program (Level 1)

- First, we created a badfile to gather information about stack-L1. Then we opened the debug file for stack-L1 (stack-L1-dbg) file in gdb to gather the information we needed.
- Then we ran the commands to gather info (things like "b bof", "run", etc). From that we got the ebp (which in our case was 0xFFFFCAE8) and the address of the start of the buffer (0xFFFFCA7C).
- With the information collected we are now able create the actual badfile with the help of exploit.py, however first we need to change some information in it.


![Terminal print of task 3_1](img/category-software/Logbook\ 5-6/Task3_1.png)
![Terminal print of task 3_2](img/category-software/Logbook\ 5-6/Task3_2.png)
![Terminal print of task 3_3](img/category-software/Logbook\ 5-6/Task3_3.png)

