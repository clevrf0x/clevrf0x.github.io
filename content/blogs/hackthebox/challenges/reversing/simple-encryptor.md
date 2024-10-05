+++
title = 'Simple Encryptor Walkthrough - HackTheBox'
date = 2024-10-05T16:25:54+05:30
description = ""

# Author
author = "Favas M"
authorTwitter = "clevrf0x"

# Categories and Tags
categories = ["Hacking", "Reversing", "Ghidra", "HackTheBox", "CTF"]
tags = ["hacking", "reversing", "ghidra", "hackthebox", "ctf", "walkthrough"]

language = "en"

# SEO
keywords = []
seoTitle = ""
+++

In this article, I’ll walk you through solving the **Simple Encryptor** reversing challenge from the platform [HackTheBox](https://hackthebox.eu). Since I haven’t done much in the realm of CTF or any kind of cybersecurity challenges for a while, my approach might not be perfect, and there may be some incorrect assumptions along the way. If you notice anything off, feel free to reach out, and I’ll be happy to correct it.

Please keep in mind that I’m primarily a developer with no formal experience in reverse engineering or tools like `Ghidra` or `IDA Pro`, and my understanding of assembly is quite limited.

### Challenge Information

Now that we've set the stage, let's dive into the challenge itself. The description provided is as follows:

> During one of our routine checks on the secret flag storage server, we discovered it had been hit by ransomware! The original flag data is gone, but luckily, we still have both the encrypted file and the encryption program itself.

Next, let's download the challenge files. There's only one file, **Simple Encryptor.zip**. After extracting it with:

```bash
unzip 'Simple Encryptor.zip'
```

We find a directory called **rev_simpleencryptor**, containing two files: an encrypted flag file named _flag.enc_ and an executable binary named _encrypt_.

To start analyzing the binary, I decided to load it into GDB to get a better sense of what’s happening. I navigated to the **rev_simpleencryptor** directory and opened the binary in GDB with:

```bash
gdb encrypt
```

Since GDB uses AT&T assembly syntax by default, I switched it to Intel format by running:

```bash
set disassembly-flavor intel
```

Next, I began the disassembly process with:

```bash
disass main
```

Here’s the initial output:

```asm
0x0000000000001289 <+0>:     endbr64
0x000000000000128d <+4>:     push   rbp
0x000000000000128e <+5>:     mov    rbp,rsp
0x0000000000001291 <+8>:     sub    rsp,0x40
0x0000000000001295 <+12>:    mov    rax,QWORD PTR fs:0x28
0x000000000000129e <+21>:    mov    QWORD PTR [rbp-0x8],rax
0x00000000000012a2 <+25>:    xor    eax,eax
0x00000000000012a4 <+27>:    lea    rsi,[rip+0xd59]        # 0x2004
0x00000000000012ab <+34>:    lea    rdi,[rip+0xd55]        # 0x2007
0x00000000000012b2 <+41>:    call   0x1170 <fopen@plt>
0x00000000000012b7 <+46>:    mov    QWORD PTR [rbp-0x28],rax
0x00000000000012bb <+50>:    mov    rax,QWORD PTR [rbp-0x28]
0x00000000000012bf <+54>:    mov    edx,0x2
0x00000000000012c4 <+59>:    mov    esi,0x0
0x00000000000012c9 <+64>:    mov    rdi,rax
0x00000000000012cc <+67>:    call   0x1160 <fseek@plt>
0x00000000000012d1 <+72>:    mov    rax,QWORD PTR [rbp-0x28]
0x00000000000012d5 <+76>:    mov    rdi,rax
0x00000000000012d8 <+79>:    call   0x1130 <ftell@plt>
0x00000000000012dd <+84>:    mov    QWORD PTR [rbp-0x20],rax
0x00000000000012e1 <+88>:    mov    rax,QWORD PTR [rbp-0x28]
```

Now, if I’m being honest, I don’t have much experience with assembly, and I quickly realized that solving this challenge purely by reading the assembly code wasn’t going to work. However, if we look closely, we can spot a few familiar function calls like `fopen`, `fseek`, and `ftell`. I recognized these functions from **`libc`**, the standard C library.

Instead of trying to interpret the assembly line by line, I decided to go the decompilation route. While we can’t get the exact original source code from a binary, tools like `Ghidra` and `IDA Pro` can generate a fairly accurate approximation of the C code from the assembly.

So, I installed `Ghidra` on my machine, created a new project, and loaded the _encrypt_ binary. After some analysis, Ghidra produced a decompiled version of the binary, which looked like this:

```c
undefined8 main(void)

{
  int iVar1;
  time_t tVar2;
  long in_FS_OFFSET;
  uint local_40;
  uint local_3c;
  long local_38;
  FILE *local_30;
  size_t local_28;
  void *local_20;
  FILE *local_18;
  long local_10;

  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_30 = fopen("flag","rb");
  fseek(local_30,0,2);
  local_28 = ftell(local_30);
  fseek(local_30,0,0);
  local_20 = malloc(local_28);
  fread(local_20,local_28,1,local_30);
  fclose(local_30);
  tVar2 = time((time_t *)0x0);
  local_40 = (uint)tVar2;
  srand(local_40);
  for (local_38 = 0; local_38 < (long)local_28; local_38 = local_38 + 1) {
    iVar1 = rand();
    *(byte *)((long)local_20 + local_38) = *(byte *)((long)local_20 + local_38) ^ (byte)iVar1;
    local_3c = rand();
    local_3c = local_3c & 7;
    *(byte *)((long)local_20 + local_38) =
         *(byte *)((long)local_20 + local_38) << (sbyte)local_3c |
         *(byte *)((long)local_20 + local_38) >> 8 - (sbyte)local_3c;
  }
  local_18 = fopen("flag.enc","wb");
  fwrite(&local_40,1,4,local_18);
  fwrite(local_20,1,local_28,local_18);
  fclose(local_18);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

### Code Explanation

> DISCLAIMER: I will be explaining the code on a line-by-line basis, so if you already understand what's going on, feel free to skip this section.

Let's observe the decompiled code closely and try to rename variables and types based on context while trying to understand how this binary encrypts data. On the first line, we have `undefined8 main(void)`, and at the end, we return '0', which means `undefined8` could be any kind of `int` type. Let's assume it is, in fact, `int`. This is how we are trying to understand the decompiled code.

If we look at line 17, we can see `local_30 = fopen("flag","rb");`. Here, we are opening a file handler for our original file and assigning it to a variable called `local_30`. Let's rename it to `original_file`.

On the line below, we call `fseek(original_file, 0, 2)`, which essentially translates to moving the cursor pointer to the end of our file. `fseek()` accepts three arguments: the first is a file handler (our `original_file`), the second is an offset value that specifies how many bytes we need to offset from the current position, and the third is the position to which we should move the cursor. So if we call `fseek(file_handler_ptr, -5, 2)`, it will move the cursor 5 bytes backward from the end of the file. The position can be one of three values: 0, 1, or 2. Here, 0 means the beginning of the file (also known as the `SEEK_SET` constant), 1 means the current cursor offset (where the cursor is currently pointing, also known as the `SEEK_CUR` constant), and 2 means the end of the file (also known as the `SEEK_END` constant).

Now that we understand what that line is doing, we can look at the next one, where we see another `libc` function being called. On line 19, we have `ftell(original_file)`. This function returns a `long int` representing the position of the file handler we passed. Since we moved the pointer to the end of the file with the previous `fseek()` call, this will essentially return the size of the file. The returned file size is stored in the variable `local_28`, so let's rename it to `file_size`.

On the next line, we move the file pointer back to the beginning using `fseek(original_file, 0, 0)`. As explained earlier, you now know what those numbers represent when passed as arguments to `fseek()`.

Next, we declare a new variable and call `malloc(file_size)` to store the result. What we're doing here is creating a temporary buffer with the same size as `original_file`. The `malloc()` function accepts a `uint` value, allocates that amount of memory on the heap, and returns a pointer to it. So, we are essentially creating a memory block on the heap to hold the contents of the file. In that line, the result pointer is stored in a variable named `local_20`, so let's rename it to `buffer`.

In the next line, we perform a simple read operation where we read all the contents of `original_file` and save them into our `buffer` variable so we can manipulate the data later. After that, we close the `original_file` handler since it’s no longer needed.

Now, let's move to the actual encryption part. We get the current time and store it in a variable named `tVar2`, so let’s rename that variable to `current_time`. We then typecast it to a `uint` and store it in the variable `local_40`, which represents how many seconds have passed since the Unix epoch. This will be our seed value for random number generation, so we can safely rename it to `seed_number`.

Now, let's talk about why we need a seed value and how random number generation (RNG) works in computer science. Despite the name, computers can’t truly generate random numbers. Instead, they use algorithms to create numbers that appear random but can be reproduced if we use the same starting point—this is the seed value. By providing a unique value as the seed, we can generate a sequence of random numbers. To reproduce that sequence, we need the exact same seed. That's why the binary generates a seed value. If we don't know the exact time when this program was executed, we won't be able to get the correct seed value.

Now that we understand how random number generation and seed values work, let's move on to the code. The code has a `for` loop that will iterate over the buffer's contents. We can rename the loop variable to `i`, as is common practice.

Inside the loop, we first generate a random number and store it in the variable `iVar1`, which we can rename to `rand_num_1`. Then, we perform a `XOR` operation on the current buffer index and overwrite that position in the buffer with the new `XOR`ed value. This operation essentially replaces the current buffer value with the result of the `XOR`. In computer science, any value that has been `XOR`ed can be reversed by performing the `XOR` operation again using the same random number.

Next, we generate another random number and store it in the variable `local_3c`, which we’ll rename to `rand_num_2`. Then, we perform a logical AND operation: `rand_num_2 = rand_num_2 & 7`. A logical AND operation is used for bitwise manipulation, and in this case, it ensures that the result will always be a number between 0 and 7, no matter what the initial random value was.

This is the most interesting part of the binary: we shift bits based on `rand_num_2`. First, we left shift using the current random value, then we right shift it. We end up with two different values. After that, we perform a logical OR operation between these values and overwrite the current buffer index with the result of the OR operation. This code block is the core of the encryption process.

The good thing is we can reverse this, assuming we have the initial random seed value generated using the current time. Now that the initial contents are encrypted, the code writes the seed value as the first 4 bytes of the encrypted file with the following code:

```c
encrypted_file = fopen("flag.enc","wb");
fwrite(&seed_number,1,4,encrypted_file);
```

Lucky us, right? After writing the seed value to the encrypted file, the code then writes the encrypted data to the file handler. So, the first 4 bytes contain the seed value, followed by the encrypted content.

Here’s the final processed `C` code:

```c
int main(void)
{
  int rand_num_1;
  time_t current_time;
  long in_FS_OFFSET;
  uint seed_number;
  uint rand_num_2;
  long i;
  FILE *original_file;
  size_t file_size;
  void *buffer;
  FILE *encrypted_file;
  long local_10;

  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  original_file = fopen("flag","rb");
  fseek(original_file,0,2);
  file_size = ftell(original_file);
  fseek(original_file,0,0);
  buffer = malloc(file_size);
  fread(buffer,file_size,1,original_file);
  fclose(original_file);
  current_time = time((time_t *)0x0);
  seed_number = (uint)current_time;
  srand(seed_number);
  for (i = 0; i < (long)file_size; i = i + 1) {
    rand_num_1 = rand();
    *(byte *)((long)buffer + i) = *(byte *)((long)buffer + i) ^ (byte)rand_num_1;
    rand_num_2 = rand();
    rand_num_2 = rand_num_2 & 7;
    *(byte *)((long)buffer + i) =
         *(byte *)((long)buffer + i) << (sbyte)rand_num_2 |

         *(byte *)((long)buffer + i) >> 8 - (sbyte)rand_num_2;
  }
  encrypted_file = fopen("flag.enc","wb");
  fwrite(&seed_number,1,4,encrypted_file);
  fwrite(buffer,1,file_size,encrypted_file);
  fclose(encrypted_file);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

Much more readable than our initial decompiled code, right? Now, let’s build an algorithm to reverse this process and retrieve the actual flag!

Your explanation of the algorithm and the code is clear and concise. Here’s a slightly refined version with some minor tweaks for clarity and structure:

### Solution

Before diving into the code for the final solution, let's first create an algorithm to reverse the encryption process to understand the flow.

#### Algorithm

1. Open the encrypted file.
2. Read the first 4 bytes as the seed value and store it in a variable for later use.
3. Get the file size, subtracting 4 bytes (the size of the seed value), as we don't need the seed in the encrypted data.
4. Allocate a temporary buffer for storing the encrypted file content using `malloc(file_size)`, and store the returned pointer in the buffer variable.
5. Seed the `rand()` function with `srand(seed_value)`.
6. Start a loop to decrypt the encrypted data.
7. Generate two random values using `rand()`: the first for reversing the `XOR`, and the second for bit shifting.
8. Perform bit shifting in reverse: first a right shift, then a left shift. After that, perform a logical OR operation and overwrite the buffer content at the current index.
9. Finally, perform the `XOR` operation again and overwrite the buffer content at the current index.
10. Repeat this for all contents and print the buffer.

#### Code

Now, let's write the code. Since we were working with `C` all this time, we'll implement the solution in `C` for consistency with the functions we've been using.

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

int main() {
  // Open encrypted file
  FILE *fp = fopen("flag.enc", "rb");

  // Read seed value
  uint32_t seed;
  fread(&seed, sizeof(seed), 1, fp);

  // Get file_size - seed value size (4 bytes) and allocate memory
  fseek(fp, 0, SEEK_END);
  long file_size = ftell(fp) - sizeof(seed);
  uint8_t *buffer = malloc(file_size);

  // Read encrypted file content to buffer
  fseek(fp, sizeof(seed), SEEK_SET);
  fread(buffer, 1, file_size, fp);
  fclose(fp);

  // Set seed value for rand()
  srand(seed);
  for (int i = 0; i < file_size; i++) {
    // generate random numbers like encrypt binary
    int rand_num_xor = rand();
    int rand_num_bitshift = rand();
    int shift_num = rand_num_bitshift & 7;

    // Reverse bit shifting and xor
    buffer[i] = (buffer[i] >> shift_num) | (buffer[i] << (8 - shift_num));
    buffer[i] = buffer[i] ^ rand_num_xor;
  }

  // Print result
  printf("%s\n", buffer);
  free(buffer);

  return 0;
}
```

Now let's compile and run the solution code:

```bash
gcc -o decrypt decrypt.c
./decrypt
```

This should print the flag in the format `HTB{vRy*******************************0r}`.
