# CTF - WEEK 4

## Challenge #1

- The purpose of this challenge is to make login to the Wordpress platform with an administrator acoount, using a CVE with known exploit to accomplish it.

1. On

We can conclude that:

- There is a _canary_ preventing the change of the return address of functions. This canary has a certain values in the beginning of the execution of the function and is located right before the return value. Before it gets to the return address of the function, the _canary_ will compare the value that is at the end of the execution in the canary address with the value stored in the beginning of the execution. If it's different, the stack changed.
- The _stack_ has execution permission (NX) - We can run files that we inject (example, shellcode instructions).
- The position on the binary aren't randomized (PIE) - we can get the addresses
  of variables/registers using a debugger, because the memeory address of all variables don't change.

### Qual é a linha do código onde a vulnerabilidade se encontra?

```c
scanf("%32s", &buffer);
printf("You gave me this: ");
printf(buffer); // vulnerability
```

### O que é que a vulnerabilidade permite fazer?

This vulnerability allows us to read from and write to memory addresses that we arent't supposed to have permissons. As so, this allows us to read the **flag** global variable.

### Qual é a funcionalidade que te permite obter a flag?

By using '%s' on `printf`'s format string, we can read the contents of the flag
char array. To do this, we just need to obtain the address of said variable.

Using the '%s format string, we ca read the contents of the flag char array. In order to do this, we need the address of the variable flag.

We will use the debugger gdb to get the address of the flag global variable.

### Flag's memory address

![Terminal print - _gdb_ debugger](CTFs/Img/Semana7-Desafio1/2.debugger-gdb.png)

- As we can see in the picture above, the address of the flag global variable is **0x804c060**.

### Exploit

![File print - exploit_example.py](CTFs/Img/Semana7-Desafio1/3.exploitFile.png)

- Now, in order to get the flag, we just need to compile the exploit_example.py file and check the result flag.

![file print - result (flag)](CTFs/Img/Semana7-Desafio1/4.result_exploit_flag.png)

`flag{5b54eade3016fad2273920d8064491}`

## Challenge #2

- We need to execute the command 'checksec program', in order to see the definition and information of the file **program**.

![Terminal print - checksec](CTFs/Img/Semana7-Desafio2/1.checksec.png)

### Qual é a linha do código onde a vulnerabilidade se encontra?

```c
scanf("%32s", &buffer);
printf("You gave me this: ");
printf(buffer); // vulnerability
```

### O que é que a vulnerabilidade permite fazer?

The vulnerability allows us to change the contents of the `key` variable. After doing this, we gain access to the restricted area of the code.

### A flag é carregada para memória? Ou existe alguma funcionalidade que podemos utilizar para ter acesso à mesma?

The flag is not loaded to memory. This _flag_ is stored in a file on the target machine. To gain access to the falg that is stored in this machine we need to know the vakue tha is stored in the _key_ global variable, which is going to activate the "backdoor" of the server. After gainning access, we can make the command _cat_ of the flag.txt inside of the shell opened, and see the pretended flag.

### Para desbloqueares essa funcionalidade o que é que tens de fazer?

- Firstly, we start by using the debugger `gdb` to get the address of the _key_ global variable. 
![Terminal print - gdb](CTFs/Img/Semana7-Desafio2/2.gdb-program.png)

- Secondly, if we write the correct number of bytes using the `printf` function. This function moves forward in the stack, trying to read something from the stack. 

- Calling various `%` commands advances the pointer of the `printf` forward. Doing this will make the printf pointer to point to the beggining og the variable pretended _key_.

![Terminal print - result (flag)](CTFs/Img/Semana7-Desafio2/3.result_exploit_flag.png)

### Flag

`flag{8d7bfe1e7b6054abd589c987c0a7e1ee}`
