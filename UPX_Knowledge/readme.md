# OUTLine 
<strong>Packer</strong> : A program that compresses a PE File into an executable form, and if it includes protection (anti-debugging, encryption, etc.) at the same time as compression, it is called a protector.
<strong>Protecter</strong> : To protect against reversing, it refers to anti-reversing techniques applied as well as decompression functions. Techniques include anti-debugging, virtual machine detection(anti-vm), code obfuscating, and polymorphic code, There are techniques such as polymorphic code, garbage code, and debugger detection.

# Intro
![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/846b3257-132c-4f9c-950f-040dd0cc5431)

Checking the difference between the UPX Packed program and the original program
1.Same the PE Header size
2.Change Section Name(".text" -> "UPX0" , ".data" -> "UPX1")
3.RawDataSize = 0 for first section(size 0 in Program)
4.Entry point is located in the second section(original program EP is located in the first section)
5.The size of the resource Section rarely changes


![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/100e48b8-6ffc-4982-ba33-5acba4d08c72)

if you look at the header of the first section of the UPX program, you can see that the VirtualSize value is set to 6000, while the Size of Raw Data value is set to 0.

why is the UPX first section empty ?
-> The compressed original code of the decompression code exists in the second section, and when the compressed file is executed by UPX, the decompression code is executed first and the original compressed code is decompressed in the first section. After the decompression process, the original EP code will run.<br>
In simple terms, UPX0 is the space where the compressed code is released after being loaded into memory, UPX1 is the space where the compressed code is stored.


# UPX analyze<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/9f1f682c-ac4c-497f-b225-6304d4a8b8b0)

UPX Program base 0x9A0000<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/cf18c6c8-fda7-4c34-b8d0-ace7bcc1de98)

if you start debugging with upx packed<br>
- pushad<br>
- mov esi, filename.9A7000<br>
- lea edi, dword ptr ds:[esi-6000]<br>
- jmp filename.9A83D2<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/0bf76749-b672-4b7d-92b7-774b96542e14)

The EAX-EDI register value is saved to the stack with the PUSHAD instruction Register values are filed into the stack, and ESP is reduced by 4 bytes * = 32 bytes, which is the size of registers.<br>
- Stored in the order of EAX, ECX, EDX, EBX, ESP, EBP, ESI, EDI<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/368d64c4-7716-4708-ad22-112e3ba80222)

PUSH ESI, 0x9A7000

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/dedf4223-76bc-445e-bcd0-a280783efab1)

PUSH edi, [esi-6000]

JMP 0x9A83D2

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/9383aed9-1c4f-48d5-a0b0-e41e2b67f0dc)

The value of filename.9A7000 in the esi register means that the UPX packer starts at the address UPX1.<br>
That is, it is the starting point of the UPX1 address.<br>
- NOTE : THE UPX Packing Program Base caught by x32dbg has a status of 0x9A0000<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/3a6db70d-95f2-405d-9df8-293af04cb62e)

[esi-6000] value 0x9A1000<br>
This is the address of the UPX0 section, which currently has no value.<br>

# Loop Unpacking - 1
![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/6f5440ec-d747-42bf-98da-a5626868422a)

Before running the program, if you look at the picture dump below, the value was no assgined.<br>
However, values are assigned by putting values into [edi] address one by one<br>

![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/f1b11fe4-ccde-4fe7-9614-03f3b31f6a5c)

This is the .text area of the original program.<br>
The values assigned one by one above are the values of the .text area of the original program.<br>

It goes through a total of 3 iteratioins like this.<br>

1)In the first loop, the data decompressed in the esi(UPX1)part is passed to al, and al passes the data to edi(UPX0) again.<br>
2)The second and third loops usually decompress the .text and .data .resource parts because data is usually stored in 3 parts: .text and .data. resource.<br>

# Loop Unpacking - 2
![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/9f505c16-225e-44d4-865e-a07f198ddcfc)

When the decompression is finished, a loop to set the IAT appears.<br>

IAT<br>
- A table describing which functions are used in which libraries by the program. The PE file specifies which libraries to import in the IMPORT_DESCRIPTOR structure.<br>

The API name string used in the original program is stored in [esi+6000] address.<br>
- Get the API start address by calling GetProcAddress(), and enter the API address in the IAT area of the original program pointed to by the EBX register<br>

# Jmp OEP
![image](https://github.com/Kwhitebear/UPXPacking_Analysis/assets/99308681/b22700f2-465c-450e-b732-6e4456c62621)

The last one goes to the original code OEP.<br>

# summary
Start : enter the address of the section to unpack<br>
- ESI : UPX1<br>
- EDI : UPX0<br>

Loop : Decompress the data that goes into the section<br>
- Loop1 : Decompress data ESI -> EDI<br>
- Loop2 : Decompress data EDX -> EDI<br>
- Loop3 : Decompress data EDX -> EDI<br>

Loop2 : Restoring the IAT<br>



