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

- The purpose of this challenge is to get the flag that is encrypted.

1. We had run the server `nc ctf-fsi.fe.up.pt 6001` in order to get the values needed (_N_, _e1_, _c1_, _e2_, _c2_). 

<img src="CTFs/Img/Semana12_13-Desafio2/1.server_values.png" alt="Server values"/><br/>

2. Then we created a python script with this values and run it.

```py
import gmpy
   
class RSAModuli:
    def __init__(self):
       self.a = 0
       self.b = 0
       self.m = 0
       self.i = 0
    def gcd(self, num1, num2):
       if num1 < num2:
           num1, num2 = num2, num1
       while num2 != 0:
           num1, num2 = num2, num1 % num2
       return num1
    def extended_euclidean(self, e1, e2):
       self.a = gmpy.invert(e1, e2)
       self.b = (float(self.gcd(e1, e2)-(self.a*e1)))/float(e2)
    def modular_inverse(self, c1, c2, N):
       i = gmpy.invert(c2, N)
       mx = pow(c1, self.a, N)
       my = pow(i, int(-self.b), N)
       self.m= mx * my % N
    def print_value(self):
       print("Plain Text: ", self.m)
   
   
def main():
   c = RSAModuli()
   N = 29802384007335836114060790946940172263849688074203847205679161119246740969024691447256543750864846273960708438254311566283952628484424015493681621963467820718118574987867439608763479491101507201581057223558989005313208698460317488564288291082719699829753178633499407801126495589784600255076069467634364857018709745459288982060955372620312140134052685549203294828798700414465539743217609556452039466944664983781342587551185679334642222972770876019643835909446132146455764152958176465019970075319062952372134839912555603753959250424342115581031979523075376134933803222122260987279941806379954414425556495125737356327411
   c1 = 0x4f1a8fb6e1869230b6bd478e2960349b10f1f8f15959048dbfff23a724a58f5842bf542bce63182f031c552e10921c56f8bd08c89aeb05199201b2136573820b24eba6806cb6fc1c86943448077b56a438b26900f534a2d58046bc4da2a0620d384ce8aaa0aa4d704ff981491022b13f9489f85b071bd2e0f147927e5fd9d158876442a756e99461f88dd3c59754f7d58476759060baa2210c38bc227febdaf1b8a983c35a228f647a8a61b08ee63fbde55957d276b09f65d2f3f277ecbd7f31d50cb111edfd638399d91a8f33b08266fa73a0a32af87162489082a54b06f052a51acd8c5e06b6545c38ba7b1f80090c912ae64c98fcd795c095c999c9157d89
   c2 = 0x7ac52fcdff363e47e5d71f45114adea5b963016bc2580bcec16714ce7bef51fc854d7487be9790782b5c1ff1178e919cd64045e202d8b7d228717fe27437d9de9996903f9d4c4bbe547549b1701ca0065e349b46a9f76a22d97bb6eaf5fb15e9776a2b456b8937900ca4bbd37d0191f4702344bda85f9cee6c8b036ee3520c3d1c28320d215992717474e0cf101ff0daf58ed88733afe51e5bbbb7e2ef548acfe58d6c08085f65055a9966ffb0c8797c386b20a071689df583cf63a55531278e69c438955cce223ec351fe4c7a352d665476a23caeb1be98be6f3c23ee19f03b64fa70b284444391bd5a4f398c612ed06d354c38ae26a6a0967cf98392c6b4cc
   e1 = 65537
   e2 = 65539
   c.extended_euclidean(e1, e2)
   c.modular_inverse(c1, c2, N)
   c.print_value()

if __name__ == '__main__':
   main()
```

<img src="CTFs/Img/Semana12_13-Desafio2/2.plain_text.png" alt="Plain text"/><br/>

3. Then, we converted the decimal received to hexadecimal, and then to text, and finally got the _flag_.

<img src="CTFs/Img/Semana12_13-Desafio2/3.decimal_to_hex.png" alt="decimal converted to hexadecimal"/><br/>

<img src="CTFs/Img/Semana12_13-Desafio2/4.hex_to_text.png" alt="hexadecimal converted to text"/><br/>

# Flag

`flag{7eca400860448759fa429047c2fbb80a}`
