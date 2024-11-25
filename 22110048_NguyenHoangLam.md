# Lab #2, 22110048, Nguyen Hoang Lam, INSE330380E_01FIE
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:  
## 1. Create 2 Virtual Machine and start them  
![image](https://github.com/user-attachments/assets/9f58e0f3-ccab-4d08-b1bd-fe2b56412070)  
![image](https://github.com/user-attachments/assets/58903387-d403-450b-b3a9-e8f3743c8f3a)

## 2. Create a text file  
*We write a text in file message.txt*
```bash
echo "Hello B" > /shared/message.txt
```

## 3. Generate RSA key pair on VM1  
*Generate the RSA public and private keys*
Private key:  
```bash
openssl genrsa -out private_key.pem 2048
```
Public key:  
```bash
openssl rsa -in private_key.pem -pubout -out public_key.pem
```

## 4. Create digital signature on VM1
*Generate a digital signature of message.txt using the private key*
```bash
openssl dgst -sha256 -sign private_key.pem -out /shared/message.sig /shared/message.txt
```
We can check if the key is created or not:  
```bash
ls
```
![image](https://github.com/user-attachments/assets/8cfb024f-72c7-4e26-b32e-6986a6a62d03)  
Then we copy the public key to shared folder:  
```bash
cp public_key.pem /shared/
```

## 5. Verify the digital signature on VM2
*Check the files received in the shared folder:*  
```bash
ls /shared
```
![image](https://github.com/user-attachments/assets/fe787321-ffc8-47c8-8298-d9a082ca6358)  
Then we verify the digital signature using the public key:
```bash
openssl dgst -sha256 -verify /shared/public_key.pem -signature /shared/message.sig /shared/message.txt
```
and the result is: Verified OK
![image](https://github.com/user-attachments/assets/082948df-9194-43f1-8de0-a3bf2774da28)  

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1. Encrypt the file on VM1
*Create original.txt file*
```bash
echo "This is a test file for hybrid encryption." > /shared/original_file.txt
```

*Generate a symmetric AES key for file encryption*  
```bash
openssl rand -out /shared/secret.key 32
```

*Encrypt the file*
```bash
openssl enc -aes-256-cbc -salt -in /shared/original_file.txt -out /shared/encrypted_file.bin -pass file:/shared/secret.key
```

*Encrypt the symmetric key*
```bash
openssl rsautl -encrypt -inkey /shared/public_key.pem -pubin -in /shared/secret.key -out /shared/encrypted_secret.key
```

## 2. Decrypt the Symmetric Key on VM2
*Decrypt the symmetric key*
```bash
openssl rsautl -decrypt -inkey /shared/private_key.pem -in /shared/encrypted_secret.key -out /shared/decrypted_secret.key
```

## 3. Decrypt the file on VM2
*Decrypt the file using the decrypted symmetric key*
```bash
openssl enc -d -aes-256-cbc -in /shared/encrypted_file.bin -out /shared/decrypted_file.txt -pass file:/shared/decrypted_secret.key
```


# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.

**Answer 1**:

## 1.

