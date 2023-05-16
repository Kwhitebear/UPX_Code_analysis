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
