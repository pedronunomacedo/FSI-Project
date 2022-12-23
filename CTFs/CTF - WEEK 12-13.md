# CTF - WEEK 12 e 13

## Challenge #1

- The purpose of this challenge is trying to decode the message provided by the `nc ctf-fsi.fe.up.pt 6000`

**1.** We start  by running the command `nc ctf-fsi.fe.up.pt 6000` and we got the encoded message.

<img src="CTFs/Img/Semana12_13-Desafio1/1.enc_flag.png" alt="Encoded message"/><br/>

**2.** By analyzing the file _template.py_, we could see that we would need to figure out the primes numbers followed by the numbers 2^512 and 2^513.

- To do this, we wrote a python script in order to find the next prime number after a given number.

```py
import random

def isMillerRabinPassed(mrc):
    maxDivisionsByTwo = 0
    ec = mrc-1
    while ec % 2 == 0:
        ec >>= 1
        maxDivisionsByTwo += 1
    assert(2**maxDivisionsByTwo * ec == mrc-1)
 
    def trialComposite(round_tester):
        if pow(round_tester, ec, mrc) == 1:
            return False
        for i in range(maxDivisionsByTwo):
            if pow(round_tester, 2**i * ec, mrc) == mrc-1:
                return False
        return True
 
    numberOfRabinTrials = 20
    for i in range(numberOfRabinTrials):
        round_tester = random.randrange(2, mrc)
        if trialComposite(round_tester):
            return False
    return True
 
def nextPrime(N):
 
    if (N <= 1):
        return 2
 
    prime = N
    found = False
 
    while(not found):
        prime = prime + 1
 
        if(isMillerRabinPassed(prime) == True):
            found = True
 
    return prime
 
N1 = 2**512
N2 = 2**513
print("1st: " + str(nextPrime(N1)))
print("---------------------------------------------")
print("2nd: " + str(nextPrime(N2)))
```

<img src="CTFs/Img/Semana12_13-Desafio1/2.prime_numbers.png" alt="Prime numbers"/><br/>

**3.** After this, we needed to calculate the _d_ value. In order to do so, we used a pytohn algorithm, found online:

```py
def mod_inverse(x,y):
    def eea(a,b):
        if b==0:return (1,0)
        (q,r) = (a//b,a%b)
        (s,t) = eea(b,r)
        return (t, s-(q*t) )

    inv = eea(x,y)[0]
    if inv < 1: inv += y # we only want positive values
    return inv
    
p = 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006084171 # next prime 2**512
q = 26815615859885194199148049996411692254958731641184786755447122887443528060147093953603748596333806855380063716372972101707507765623893139892867298012168351 # next prime 2**513

print(mod_inverse(p, q))
```

<img src="CTFs/Img/Semana12_13-Desafio1/3.value_of_d.png" alt="Prime numbers"/><br/>

**4.** After getting the value of _p_, _q_ and _d_, we write replace them in the fields required in the given file _template.py_, and run the script.

```py
from binascii import hexlify, unhexlify

p = 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006084171
q = 26815615859885194199148049996411692254958731641184786755447122887443528060147093953603748596333806855380063716372972101707507765623893139892867298012168351
n = p*q
e = 0x10001 # a constant
d = 32148745104617719611469860837520069095243013547461161624365705436392549353428328854051450263424947795515997965956157657970803482741943111508623685194329747704158900209679524603520884720244523165529357010257764632981617985618265704337977264403534764689557688026111797495540714181081554773530395510587656455823

enc_flag = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004cafa962cf864ca0b3ef9318453f20a61d0fe80694743fe8271d5c5fd09b5af91b142dd84c3c61c2863e4346c2f9325b72e790f99c5042cf24ab3fb2eed4417e46227648ac0af64d9498cd88366da57b58b085a8ceeed2c8111d4a19e7d50f9b714faf77d5121414abd1bde1ecc7fafab2516375936759bca051067353f88948"

def enc(x):
	int_x = int.from_bytes(x, "big")
	y = pow(int_x,e,n)
	return hexlify(y.to_bytes(256, 'big'))

def dec(y):
	int_y = int.from_bytes(unhexlify(y), "big")
	x = pow(int_y,d,n)
	return x.to_bytes(256, 'big')

y = dec(enc_flag)
print(y.decode())
```

**5.** After that, the _flag_ was printed in the terminal.

<img src="CTFs/Img/Semana12_13-Desafio1/4.flag_result" alt="Flag result"/><br/>

# Flag

`flag{61752de15f970397d3aa2113d22c0bf7}`

## Challenge #2

# Flag

`flag{4c6a300c7ccb7d3734acf1b5d8239e58}`
