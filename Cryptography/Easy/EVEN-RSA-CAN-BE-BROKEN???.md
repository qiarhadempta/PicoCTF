#EVEN RSA CAN BE BROKEN???
## Challenge Overview
This service provides you an encrypted flag. Can you decrypt it with just N & e?

## Hints
1. How much do we trust randomness?
2. Notice anything interesting about N?
3. Try comparing N across multiple requests

Challenge link: https://learn.cylabacademy.org/library/470?page=4&workspace=true

<img width="1388" height="660" alt="image" src="https://github.com/user-attachments/assets/75e1b0b9-137d-48d4-a036-b6e395fdbe3f" />

## Solutions
The RSA values I've got:
```bash
N: 18307451923569260405504585123413635543345207403849877037689544201728025893879384680295209834422578221042359840092847627086693889417819273641851594529708614
e: 65537
cyphertext: 16774913176908615139039130693826151598701253408581260827690215508311298933373074765610669804579019996438019628839388888842987278499095341863707135986358823
```

Looking at the N, I suspect that the N is relatively too small for RSA, so basically we can just factorize it quickly. I'm using this following python script to find p, calculates Euler's totient φ(n), computes the modular inverse to get d, and reveals the flag or plaintext:
```python
import math

def decrypt_rsa(n, e, c):
    print("[*] Factoring n to find p and q...")
    p = None
    q = None
  
  
    if n % 2 == 0:
        p = 2
        q = n // 2
    else:
        for i in range(3, int(math.isqrt(n)) + 1, 2):
            if n % i == 0:
                p = i
                q = n // i
                break
                
    if not p:
        raise ValueError("[-] Failed to factorize n. Modulus might be too large.")
        
    print(f"[+] Found Primes:\n    p = {p}\n    q = {q}")
    
    phi = (p - 1) * (q - 1)
    
  
    try:
        d = pow(e, -1, phi)
    except ValueError:
        raise ValueError("[-] e and phi are not coprime. Cannot compute d.")
        
    print(f"[+] Private Key d = {d}")
    
    m = pow(c, d, n)
    print(f"[+] Numeric Message m = {m}")
    
    try:
        # Convert the integer back to bytes, then decode to UTF-8
        num_bytes = (m.bit_length() + 7) // 8
        plaintext_bytes = m.to_bytes(num_bytes, byteorder='big')
        plaintext = plaintext_bytes.decode('utf-8', errors='ignore')
        print(f"[+] Decrypted Text: {plaintext}")
        return plaintext
    except Exception:
        print("[-] Could not convert message to string. Outputting integer representation.")
        return m

KNOWN_N = 18307451923569260405504585123413635543345207403849877037689544201728025893879384680295209834422578221042359840092847627086693889417819273641851594529708614# Example small modulus (61 * 53)
KNOWN_E = 65537        # Public exponent
KNOWN_C = 16774913176908615139039130693826151598701253408581260827690215508311298933373074765610669804579019996438019628839388888842987278499095341863707135986358823# Ciphertext

decrypt_rsa(KNOWN_N, KNOWN_E, KNOWN_C)
```
<img width="1434" height="714" alt="image" src="https://github.com/user-attachments/assets/30ce1ef6-4280-418e-aa2b-92eb309c23c6" />

Flag: `picoCTF{tw0_1$_pr!m31c9046c4}`
