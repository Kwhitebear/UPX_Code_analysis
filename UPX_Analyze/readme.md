# UPX packing program Analysis

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/92f9ca76-0896-4100-b8d8-45e544579247)

This is Packing & Unpacking Picture. If you look at the file size, if you unpack it, it will return to the original size, and if you pack it, the size will be reduced.<br>

# Before Using Upx Check

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/1f5183ad-a301-4c4a-872b-438172a725a7)

Logic to check file type and size.<br>

# UPX Packing Analyze


<strong>CommandLine : upx.exe cli.exe</strong>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/41b7fe28-d957-4d25-ad0d-6b2f04570618)

Checking Unpacking & Packing file size.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/6c45acb9-9214-4bf7-b177-15f7534d1a63)

setting : EAX = 1<br>

Packing goes into an if statement.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/b50d1a9e-5a13-47d9-acd2-9c9b7f064c37)

Prepare to put the packing data values into the previously created cli2.upx file<br>

Continue going inside upx1.4CD1B0 and start tracing.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/d170f533-d1c4-4476-b841-8b1290e36e40)

This is the address to insert data into the cli2.upx file to obtain and pack the location and value of each section of the file.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/a791d56e-5b8a-430d-83cc-009ae9f3c58a)

First. PE data values are obtained.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/38f61c4b-d911-41a7-bc78-741a921d816d)

Get the file offset when packed.
- The Picture right above is the packed file Section UPX if you check pFile 400.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/39fcb6bd-15e4-4c34-8890-dc0e1b7deebd)

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/5b496812-c11c-4d07-ad61-1667f14a3620)

Finding the location and size of Sections while looping through loops<br>
- .textbss address<br>
- .reloc address<br>
Retrieve Section values between .textbss address and .reloc address. In other words, you can think of finding the position of the first coming Section and the last Section.<br>

The Section calculation formula is shown in the picture below.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/b6a7c1d5-54dc-4018-baad-8ef96d21cbff)

Next Section = rbp + rdi(textbss address+ section offset)<br>

The next Scetions are obtained by adding each section offset based on the first section.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/3306bb93-39e6-4461-9856-c0860124014d)

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/73fd073a-6149-4906-a3d2-951e09950ba9)

Continue tracing and come to the place where the PE header size is checked.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/48caf29d-612a-4edc-95a0-45fea5add17d)

Checked cli2.upx file, "MS-DOS Stub Program(DOS Stub) is writeen.<br>

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/3815b658-8109-4629-a475-14ce68b7b03e)

Name = UPX0, Name = UPX1 and Size This is the logic to set the size, which can be seen when packing.<br>


# Packing Start

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/edcc916b-592d-4dbc-888e-6076319346c6)



# UPX Unpacking Analyze

![image](https://github.com/Kwhitebear/UPX_Code_analysis/assets/99308681/ffdb94bb-2937-415e-a3c5-0ec24aa94580)

<string>commandLine : upx.exe -d cli.exe</string>

Setting : Eax = 2









