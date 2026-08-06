# unpackme.py

<img width="587" height="314" alt="image" src="https://github.com/user-attachments/assets/2095702d-1758-4d53-a8a4-bcb6269b840b" />

## Solution
In this challenge, we are provided with the following Python script:
```python
import base64
from cryptography.fernet import Fernet

payload = b'gAAAAABkzWGSzE6VQNTzvRXOXekQeW4CY6NiRkzeImo9LuYBHAYw_hagTJLJL0c-kmNsjY33IUbU2IWlqxA3Fpp9S7RxNkiwMDZgLmRlI9-lGAEW-_i72RSDvylNR3QkpJW2JxubjLUC5VwoVgH62wxDuYu1rRD5KadwTADdABqsx2MkY6fKNTMCYY09Se6yjtRBftfTJUL-LKz2bwgXNd6O-WpbfXEMvCv3gNQ7sW4pgUnb-gDVZvrLNrug_1YFaIe3yKr0Awo0HIN3XMdZYpSE1c9P4G0sMQ=='

key_str = 'correctstaplecorrectstaplecorrec'
key_base64 = base64.b64encode(key_str.encode())
f = Fernet(key_base64)
plain = f.decrypt(payload)
exec(plain.decode())
```

The script encrypts its underlying code logic using Fernet symmetric encryption to prevent static analysis. At runtime, it decrypts the payload back into plain text and passes it directly to exec(), which executes the decrypted Python string dynamically.
To solve this problem, we can simply inspect/deobfuscate the payload by replacing or appending a print() statement to display the decrypted plaintext code:
```python
print(plain.decode())
```

Output:
```bash
What's the password? 
That password is incorrect.

pw = input('What\'s the password? ')

if pw == 'batteryhorse':
  print('picoCTF{175_chr157m45_cd82f94c}')
else:
  print('That password is incorrect.')
```

Flag: `picoCTF{175_chr157m45_cd82f94c}`
