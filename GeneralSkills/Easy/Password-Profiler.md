# Password Profiler
## Challenge Overview
We intercepted a suspicious file from a system, but instead of the password itself, it only contains its SHA-1 hash. Using OSINT techniques, you are provided with personal details about the target. Your task is to leverage this information to generate a custom password list and recover the original password by matching its hash.

Challenge link: https://learn.cylabacademy.org/library/712?page=3
<img width="1011" height="750" alt="image" src="https://github.com/user-attachments/assets/da86a7be-8953-469c-9718-5771abe784c2" />

## Hints
[CUPP](https://github.com/Mebus/cupp) is a Python tool for generating custom wordlists from personal data.


## Solutions
In this challenge, we were given 3 files:

`userinfo.txt`
```bash
First Name: Alice
Surname: Johnson
Nickname: AJ
Birthdate: 15-07-1990
Partner's Name: Bob
Child's Name: Charlie
```

`hash.txt`
```bash
First Name: Alice
Surname: Johnson
Nickname: AJ
Birthdate: 15-07-1990
Partner's Name: Bob
Child's Name: Charlie
```

`check_password.py`
```python
#!/usr/bin/env python3
import hashlib

HASH_FILE = "hash.txt"
WORDLIST_FILE = "passwords.txt" # wordlist that was generated using CUPP

def load_hash():
    with open(HASH_FILE, "r") as f:
        return f.read().strip()

def crack_password(target_hash):
    with open(WORDLIST_FILE, "r", encoding="utf-8", errors="ignore") as f:
        for password in f:
            password = password.strip()
            if hashlib.sha1(password.encode()).hexdigest() == target_hash:
                return password
    return None

if __name__ == "__main__":
    target_hash = load_hash()
    result = crack_password(target_hash)
    if result:
        print(f"Password found: picoCTF{{{result}}}")
    else:
        print("No match found.")
```

So, to solve this challenge, 'm using CUPP to create targeted wordlists for password testing. 

First, clone the repository and run CUPP in interactive mode:
```bash
git clone https://github.com/Mebus/cupp.git
cd cupp
python3 cupp.py -i
```

CUPP will ask for the victim's information. We fill in the fields using the data from `userinfo.txt`:
```bash
First Name: Alice
Surname: Johnson
Nickname: AJ
Birthdate (DDMMYYYY): 15071990

Partner's Name: Bob
Child's Name: Charlie
```

For the command above, I enabled additional combinations such as special characters, random numbers, concatenated words, and leetspeak substitutions. 
These options will increase the number of generated password candidates and improve the chances of finding the correct password.
<img width="753" height="505" alt="image" src="https://github.com/user-attachments/assets/9561313c-eae0-4b2a-9a27-ebfc909e9462" />

After the questionnaire is complete, CUPP generates a customized wordlist. I renamed it to `passwords.txt` so it matches the filename expected by the provided script:
```bash
mv alice.txt passwords.txt
```

Next, I placed the intercepted SHA-1 hash inside `hash.txt`:
```bash
968c2349040273dd57dc4be7e238c5ac200ceac5
```

and now, we can execute the supplied password-checking script:
```bash
python3 check_password.py
```

The script reads every password candidate from passwords.txt, computes its SHA-1 hash using Python's hashlib library, and compares it against the target hash stored in hash.txt. Once a matching hash is found, the corresponding plaintext password is printed and formatted as the flag:
```bash
Password found: picoCTF{<password>}
```
<img width="971" height="463" alt="image" src="https://github.com/user-attachments/assets/b89395a1-10d2-4b9c-bbca-90a731f589f2" />

Flag found: `picoCTF{Aj_15901990}`
