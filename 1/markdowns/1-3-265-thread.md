
# Post \#61607367 [Link](https://stackoverflow.com/questions/61607367/)

## How to encrypt JSON in python

**Vote**: 5 (499/702) **Views**: 14527 (364/702) 

**Internal ID** \#1-3-265

Created at 2020-05-05 06:53:44

Tags: `python` `json` `encryption` `data-security`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a JSON file. I am running a program, in python, where data is extracted from the JSON file. Is there any way to encrypt the JSON file with a key, so that if someone randomly opens the file, it would be a mess of characters, but when the key is fed to the program, it decrypts it and is able to read it? Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-05-05 07:28:37

------------

Yes, you can encrypt a .json file. Make sure you install the cryptography package by typing
```
pip install cryptography
# or on windows:
python -m pip install cryptography
```

Then, you can make a program similar to mine:
```
#this imports the cryptography package
from cryptography.fernet import Fernet

#this generates a key and opens a file 'key.key' and writes the key there
key = Fernet.generate_key()
with open('key.key','wb') as file:
    file.write(key)

#this just opens your 'key.key' and assings the key stored there as 'key'
with open('key.key','rb') as file:
    key = file.read()

#this opens your json and reads its data into a new variable called 'data'
with open('filename.json','rb') as f:
    data = f.read()

#this encrypts the data read from your json and stores it in 'encrypted'
fernet = Fernet(key)
encrypted = fernet.encrypt(data)

#this writes your new, encrypted data into a new JSON file
with open('filename.json','wb') as f:
    f.write(encrypted)
```

Note that this block:
```
with open('key.key','wb') as file:
    file.write(key)

#this just opens your 'key.key' and assigns the key stored there as 'key'
with open('key.key','rb') as file:
    key = file.read()
```

isn't necessary. It is just a way to store the generated key in a safe place, and read it back in. You can delete that block if you want.
Let me know if you need further assistance :)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-04-17 12:10:50

------------

you can do it with Asymmetric Encryption and Decryption.
here is full article [article](https://nitratine.net/blog/post/asymmetric-encryption-and-decryption-in-python/)
keep this in mind you should encode your string data before encryption
to read the json data :
```
data = json.load(open('data.json'))
```

for encryption:
```
python -m pip install cryptography
```

to create/store keys and encrypt the json data you should do this:
```
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.asymmetric import rsa
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import padding

def create_keys():
    private_key = rsa.generate_private_key(
        public_exponent=65537,
        key_size=2048,
        backend=default_backend()
    )
    public_key = private_key.public_key()

    pem = private_key.private_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PrivateFormat.PKCS8,
        encryption_algorithm=serialization.NoEncryption()
    )
    with open('private_key.pem', 'wb') as f:
        f.write(pem)
    pem = public_key.public_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PublicFormat.SubjectPublicKeyInfo
    )
    with open('public_key.pem', 'wb') as f:
        f.write(pem)


def get_private_key():
    with open("private_key.pem", "rb") as key_file:
        private_key = serialization.load_pem_private_key(
            key_file.read(),
            password=None,
            backend=default_backend()
        )
    return private_key

def get_public_key():
    with open("public_key.pem", "rb") as key_file:
        public_key = serialization.load_pem_public_key(
        key_file.read(),
        backend=default_backend()
    )
    return public_key

def encrypt():
    # createKeys()
    public_key = get_public_key()
    massage = str(data).encode('utf-8')
    encrypted = public_key.encrypt(massage, padding.OAEP(padding.MGF1(hashes.SHA256()), hashes.SHA256(), None))
    private_key = get_private_key()
    original_message = private_key.decrypt(encrypted, padding.OAEP(padding.MGF1(hashes.SHA256()), hashes.SHA256(), None))

    return original_message.decode('utf-8')
```

and for decryption you should do this:
```
original_message = private_key.decrypt(
    encrypted,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-10-24 00:08:30

------------

I want to keep this question up-to-date because it's useful, this my implementation:
```
from cryptography.fernet import Fernet as F


def encrypt_message(key: str, msg: Union[str, bytes]) -> str:
    encoded_key = key if isinstance(key, bytes) else key.encode()
    handler = F(encoded_key)
    encoded_msg = msg if isinstance(msg, bytes) else msg.encode()
    treatment = handler.encrypt(encoded_msg)
    return str(treatment, 'utf-8')

def decrypt_message(key: str, msg: Union[str, bytes]) -> str:
    encoded_key = key if isinstance(key, bytes) else key.encode()
    handler = F(encoded_key)
    encoded_msg = msg if isinstance(msg, bytes) else msg.encode()
    treatment = handler.decrypt(encoded_msg)
    return str(treatment, 'utf-8')
```



------------
    
    