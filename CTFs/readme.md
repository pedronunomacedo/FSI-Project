## CTF

### Challenge 1

Initially we ran the provided code <kbd>nc ctf-fsi.fe.up.pt 6000 </kbd> in the shell and got an encrypted sequence of numbers and letters.

![](https://i.imgur.com/EhOQIBI.png)

By analyzing the provided python code we realized that it would be necessary to calculate the prime number greater than 2^512 and 2^513. For this we also used a python code that returned this number.

```
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
 
N = 2**512 #or 2**513
print(nextPrime(N))
```

So all that remains is to find out the value of d. To do this, we used a python algorithm, found online. 
```
def mod_inverse(x,y):
	def eea(a,b):
		if b==0:return (1,0)
		(q,r) = (a//b,a%b)
		(s,t) = eea(b,r)
		return (t, s-(q*t) )

	inv = eea(x,y)[0]
	if inv < 1: inv += y #we only want positive values
    return inv
```

Finally, we replaced all the values in the executable we were given and ran the code to find the flag.

![](https://i.imgur.com/zHDUodO.png)


flag:
```
flag{901f3ab61335e9f64f3cfe6147a8d2c4}
```


### Challenge 2

As with the previous CTF, we start by running the provided code <kbd>nc ctf-fsi.fe.up.pt 6001</kbd> in the shell and we had three encrypted values, N (which was the same for both messages) and two different values of E.

![](https://i.imgur.com/9vViFdQ.png)

To do this we used some python code, found online, and replaced it with the values we got from the shell.
```
import gmpy2
   
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
       self.a = gmpy2.invert(e1, e2)
       self.b = (float(self.gcd(e1, e2)-(self.a*e1)))/float(e2)
    def modular_inverse(self, c1, c2, N):
       i = gmpy2.invert(c2, N)
       mx = pow(c1, self.a, N)
       my = pow(i, int(-self.b), N)
       self.m= mx * my % N
    def print_value(self):
       print("Plain Text: ", self.m)
   
   
def main():
   c = RSAModuli()
   N = 29802384007335836114060790946940172263849688074203847205679161119246740969024691447256543750864846273960708438254311566283952628484424015493681621963467820718118574987867439608763479491101507201581057223558989005313208698460317488564288291082719699829753178633499407801126495589784600255076069467634364857018709745459288982060955372620312140134052685549203294828798700414465539743217609556452039466944664983781342587551185679334642222972770876019643835909446132146455764152958176465019970075319062952372134839912555603753959250424342115581031979523075376134933803222122260987279941806379954414425556495125737356327411
   c1 = 0x9d331ad71ee40f619840a2a92f1287307d0070bbc0e860cf8f5ee7d3b167749b989e9dd326f6837c74206ddaaa655f94aa0fdb2391cc9f8d5d69db0521a9148e4efe1121a0fb2acff146a8318d17695a2953fe4661bac9698d71396a1586824277925ae495576666ccfe5b93ddf270f917f02e8a6855a19797cc95fbb6a702c14d4b6c40bd18935ff25322ce1302ccf902bd2227ebdbe809d208bb5c09988cc7b51cf30aa429b11d0cb1bcbbce8b5ff88feea57f3a32415e755881ff55ce41f38465290e09dbea2a1bb8cbd801018772caaf1426bd2c8708cdd2b6cf9c67bda8a818721de5b7f561b1ee37e8161a5d794e966296c82aad997e92fa7e625f630d
   c2 = 0xbdb92c72b74448c305b8b45acaa76e0897a0c8296956b15ca30752a649c0ffe6453f33eef4e01770fdfde4519c24e68daf8c2d85d0439202f728acab8560ce1d9c67161c1580189c94548769997cca81486379db3a693a22ca1325cc1cf2093cae5a821439b9af60d61a866cf4f360187ec31bf6991259f247ee9d6b538b3bd6efaaa668d06fbbc75c8176aac02b68d5021f94e61b1e17e33117bf585bdad1fea496f68e5ad487c310c692a98745ebc80defab42f9845b6db1d4f429dfbf5ad4aec60d90f40ceb011a65aa6bba942e1ddb89155e0c7cedb05982da28b8bd09107d05b022a2e430c3ad91b3b51af608e82391f650031b3411f6f1aa54dc6bc39c
   e1 = 65537
   e2 = 65539
   c.extended_euclidean(e1, e2)
   c.modular_inverse(c1, c2, N)
   c.print_value()

if __name__ == '__main__':
   main()
```

After running the code we got the output of the function, the Plain Text in binary:

![](https://i.imgur.com/TXjVjZn.png)

We first converted the Plain Text to hexadecimal and finally to text and got the flag.

![](https://i.imgur.com/SDKhqNw.png)


flag:
```
flag{986037b8a0f76edbd349a9e777477f11}
```

### Challenge 2

As with the previous CTF, we start by running the provided code <kbd>nc ctf-fsi.fe.up.pt 6001</kbd> in the shell and we had three encrypted values, N (which was the same for both messages) and two different values of E.

![](https://i.imgur.com/9vViFdQ.png)

To do this we used some python code, found online, and replaced it with the values we got from the shell.
```
import gmpy2
   
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
       self.a = gmpy2.invert(e1, e2)
       self.b = (float(self.gcd(e1, e2)-(self.a*e1)))/float(e2)
    def modular_inverse(self, c1, c2, N):
       i = gmpy2.invert(c2, N)
       mx = pow(c1, self.a, N)
       my = pow(i, int(-self.b), N)
       self.m= mx * my % N
    def print_value(self):
       print("Plain Text: ", self.m)
   
   
def main():
   c = RSAModuli()
   N = 29802384007335836114060790946940172263849688074203847205679161119246740969024691447256543750864846273960708438254311566283952628484424015493681621963467820718118574987867439608763479491101507201581057223558989005313208698460317488564288291082719699829753178633499407801126495589784600255076069467634364857018709745459288982060955372620312140134052685549203294828798700414465539743217609556452039466944664983781342587551185679334642222972770876019643835909446132146455764152958176465019970075319062952372134839912555603753959250424342115581031979523075376134933803222122260987279941806379954414425556495125737356327411
   c1 = 0x9d331ad71ee40f619840a2a92f1287307d0070bbc0e860cf8f5ee7d3b167749b989e9dd326f6837c74206ddaaa655f94aa0fdb2391cc9f8d5d69db0521a9148e4efe1121a0fb2acff146a8318d17695a2953fe4661bac9698d71396a1586824277925ae495576666ccfe5b93ddf270f917f02e8a6855a19797cc95fbb6a702c14d4b6c40bd18935ff25322ce1302ccf902bd2227ebdbe809d208bb5c09988cc7b51cf30aa429b11d0cb1bcbbce8b5ff88feea57f3a32415e755881ff55ce41f38465290e09dbea2a1bb8cbd801018772caaf1426bd2c8708cdd2b6cf9c67bda8a818721de5b7f561b1ee37e8161a5d794e966296c82aad997e92fa7e625f630d
   c2 = 0xbdb92c72b74448c305b8b45acaa76e0897a0c8296956b15ca30752a649c0ffe6453f33eef4e01770fdfde4519c24e68daf8c2d85d0439202f728acab8560ce1d9c67161c1580189c94548769997cca81486379db3a693a22ca1325cc1cf2093cae5a821439b9af60d61a866cf4f360187ec31bf6991259f247ee9d6b538b3bd6efaaa668d06fbbc75c8176aac02b68d5021f94e61b1e17e33117bf585bdad1fea496f68e5ad487c310c692a98745ebc80defab42f9845b6db1d4f429dfbf5ad4aec60d90f40ceb011a65aa6bba942e1ddb89155e0c7cedb05982da28b8bd09107d05b022a2e430c3ad91b3b51af608e82391f650031b3411f6f1aa54dc6bc39c
   e1 = 65537
   e2 = 65539
   c.extended_euclidean(e1, e2)
   c.modular_inverse(c1, c2, N)
   c.print_value()

if __name__ == '__main__':
   main()
```

After running the code we got the output of the function, the Plain Text in binary:

![](https://i.imgur.com/TXjVjZn.png)

We first converted the Plain Text to hexadecimal and finally to text and got the flag.

![](https://i.imgur.com/SDKhqNw.png)


flag:
```
flag{986037b8a0f76edbd349a9e777477f11}
```

