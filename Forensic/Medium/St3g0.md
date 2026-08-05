# St3g0

<img width="997" height="593" alt="image" src="https://github.com/user-attachments/assets/57ea57d2-fcc7-4252-bdc0-ac93be0506a6" />


## Solutions

At first, I'm trying to use steghide to recover the secret data that might be hidden in the file, but then I just happen to know that steghide doesn't work on PNG files!

So, to solve this challenge, I'm using zsteg to analyzes pixel bit-planes, LSB permutations, and color/alpha channels to find the hidden patterns.

```bash
zsteg -a pico.flag.png
```

Flag found: `picoCTF{7h3r3_15_n0_5p00n_a1062667}`

<img width="1920" height="981" alt="image" src="https://github.com/user-attachments/assets/62ebff72-a252-4a88-9dab-0b58f2be7a3b" />
