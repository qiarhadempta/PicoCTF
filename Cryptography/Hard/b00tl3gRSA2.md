# b00tl3gRSA2
## Challenge Overview

In RSA d is a lot bigger than e, why don't we use d to encrypt instead of e?

Challenge link: https://learn.cylabacademy.org/library/2?page=1&search=rsa

<img width="1007" height="765" alt="image" src="https://github.com/user-attachments/assets/a90ecd29-44ff-495f-b7df-e441eaf079b3" />

## Solutions
RSA values I received:

```bash
c: 92483590805616961251752938742597595652779553477153525222191692473700517713879885759519471511643555357727383185772927487989941458888386934028600277816891619503701464231482489884996000529894336305366948949079497296122522697408202508582255199136335328326897227851006846571069613313109412600003376336323800883197
n: 164743704338390285012762596251725017271021324583302282702851543165801065907928799851539324679757607479856277284920245069100613552595530350167581672714720579839906466489687871372519716829426138358508239012221789771041398649641426630755755134874789155958751427428566168284615943605349591760346772958179579336261
e: 50649570452022916104218282067778619298927455773516604272086847784422931732896797049127452476806018479814824160609396492016849451016179273166708929666126072992153358092677625894093190491463508113888703281457139998386985997921848946379282831252619655362704640305303157622668115325918757068353446393585471477537
```

Analyzing the given parameters reveals that the public exponent $e$ is exceptionally large, nearly matching the size of the modulus $N$. In standard RSA configurations, a small public exponent like $e = 65537$ is preferred to speed up encryption, which in turn causes the private exponent $d$ to be large. However, the hint in the prompt implies what happens when the roles are flipped: if an implementation uses a small private exponent $d$ to make decryption faster, the corresponding public exponent $e$ scales up drastically.When $d < \frac{1}{3} N^{1/4}$, the cryptosystem becomes vulnerable to Wiener's Attack. Because $e \cdot d - k \cdot \phi(N) = 1$, dividing both sides by $d \cdot N$ shows that the fraction $\frac{e}{N}$ closely approximates $\frac{k}{d}$. Using the method of continued fractions, we can compute the convergents of $\frac{e}{N}$ to recover the hidden small private exponent $d$ without needing to factorize $N$ directly.Attempting a direct modular exponentiation using $c^e \bmod N$ fails because $c$ was generated using $d$, meaning we strictly require $d$ to reverse the ciphertext back to plaintext ($m = c^d \bmod N$). Implementing Wiener's attack in Python allows us to generate the convergents of $\frac{e}{N}$, iterate through the candidates for $d$, and validate the correct key by checking quadratic roots against $\phi(N)$. Once $d$ is recovered, executing $m = c^d \bmod N$ and decoding the integer output directly yields the plaintext flag.

```python
import math

def bytes_to_long(b):
    return int.from_bytes(b, 'big')

def long_to_bytes(n):
    return n.to_bytes((n.bit_length() + 7) // 8, 'big')

c = 92483590805616961251752938742597595652779553477153525222191692473700517713879885759519471511643555357727383185772927487989941458888386934028600277816891619503701464231482489884996000529894336305366948949079497296122522697408202508582255199136335328326897227851006846571069613313109412600003376336323800883197
N = 164743704338390285012762596251725017271021324583302282702851543165801065907928799851539324679757607479856277284920245069100613552595530350167581672714720579839906466489687871372519716829426138358508239012221789771041398649641426630755755134874789155958751427428566168284615943605349591760346772958179579336261
e = 50649570452022916104218282067778619298927455773516604272086847784422931732896797049127452476806018479814824160609396492016849451016179273166708929666126072992153358092677625894093190491463508113888703281457139998386985997921848946379282831252619655362704640305303157622668115325918757068353446393585471477537

def continued_fraction(e, n):
    cf = []
    while n:
        q = e // n
        cf.append(q)
        e, n = n, e - q * n
    return cf

def convergents(cf):
    convs = []
    n0, n1 = 0, 1
    d0, d1 = 1, 0
    for q in cf:
        n2 = q * n1 + n0
        d2 = q * d1 + d0
        convs.append((n2, d2))
        n0, n1 = n1, n2
        d0, d1 = d1, d2
    return convs

def wieners_attack(e, n):
    cf = continued_fraction(e, n)
    convs = convergents(cf)
    
    for k, d in convs:
        if k == 0 or d % 2 == 0:
            continue
        phi = (e * d - 1) // k
        b = n - phi + 1
        discr = b * b - 4 * n
        if discr >= 0:
            s = math.isqrt(discr)
            if s * s == discr and (b + s) % 2 == 0:
                return d
    return None

d = wieners_attack(e, N)

if d:
    m = pow(c, d, N)
    flag = long_to_bytes(m)
    print("\n[FLAG]:", flag.decode(errors='ignore'))
else:
    print("[-] failed.")
```

Flag: `picoCTF{bad_1d3a5_3801255}`
