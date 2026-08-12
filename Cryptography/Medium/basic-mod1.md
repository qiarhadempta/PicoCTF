# basic-mod1
## Challenge Overview
We found this weird message being passed around on the servers, we think we have a working decryption scheme.

Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.

Wrap your decrypted message in the picoCTF flag format (i.e. picoCTF{decrypted_message})

<img width="1012" height="768" alt="image" src="https://github.com/user-attachments/assets/0ab30283-81bc-4494-877b-d4b25d2cddd5" />

## Solutions

To solve this challenge, I wrote a Python script to automate the decryption process based on the given rules. First, I constructed a character set mapping using the string module, combining uppercase letters (A-Z for indices 0–25), decimal digits (0-9 for indices 26–35), and an underscore (_ for index 36). Then, I stored all the given numbers into a list and iterated through each integer, applying the modulo operation n % 37 to retrieve the corresponding character from the defined character set. Finally, joining all mapped characters produced the inner decrypted message, which was then wrapped inside the standard picoCTF{} flag format.
```python
import string

charset = string.ascii_uppercase + string.digits + "_"

numbers = [128, 322, 353, 235, 336, 73, 198, 332, 202, 285, 57, 87, 262, 221, 218, 405, 335, 101, 256, 227, 112, 140]  

flag = "".join(charset[n % 37] for n in numbers)

print(flag)
```

Flag: `picoCTF{R0UND_N_R0UND_79C18FB3}`
