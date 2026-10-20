# Lab 3: AES Encryption Modes Using OpenSSL

## Objectives

- Practice text encryption and decryption using OpenSSL.
- Understand the use of initialization vectors in encryption modes.
- Compare the visual effects of AES-CBC and AES-ECB on BMP images.
- Examine encrypted and decrypted file sizes.
- Understand padding differences between CBC and OFB.

## Tools

- OpenSSL
- Linux Terminal
- cat
- xxd
- head
- tail
- ls
- Image Viewer (eog)

## Task1 :

We wrote the text message "This is my top secret" in a plaintext.txt file.
We encrypted the contents of the plaintext.txt file using the AES-128-ECB algorithm and its key is 00112233445566778899AABBCCDDEEFF.
We used the key derivation function -pbkdf2.
We put the encrypted text into ciphertext.txt and displayed it.
Then we decrypted it the same way we encrypted it but by changing -e to -d and put it in decrypted.txt file.
We have displayed the decrypted.txt file.

<img width="1031" height="693" alt="task1 lab3" src="https://github.com/user-attachments/assets/0c761432-0c16-46eb-9093-5b36affcd9c1" />


**Image explanation:** The commands create plaintext.txt, encrypt it, inspect the ciphertext using cat and xxd, and decrypt it into decrypted.txt. The recovered text is “This is my top secret”.

**Clarification:** The screenshot uses lowercase -k, which supplies a password rather than a raw hexadecimal AES key. Therefore, the text 00112233445566778899AABBCCDDEEFF is used as a password, and PBKDF2 derives the encryption key from it. Uppercase -K is used to provide a raw hexadecimal key.

### What is openssl program using for ?

openSSL is used for implementing cryptographic operations such as encryption, decryption, key generation, certificate management, and secure communication using protocols like SSL/TLS.

### Do all Encryption mode use iv ?

No, not all encryption modes use IV. Only certain modes, such as CBC, CFB, and OFB, require an IV.

### Name the mode which don’t use iv ?

ECB (Electronic Codebook) mode does not use an IV.

## Task 2 :

In this task, we encrypted a BMP image using the AES encryption algorithm in CBC (Cipher Block Chaining) mode via the openssl tool.
The following steps were performed:

1. Encrypting the image: Using openssl enc -aes-128-cbc with a predefined key and IV.
2. Extracting the image header: The first 54 bytes (header) were separated to preserve the image format.
3. Encrypting the image body: Only the pixel data was encrypted to distort the image content.
4. Reconstructing the encrypted image: The original header was combined with the encrypted body to form a new BMP file.

Result:
The output image appeared as visual noise, proving that AES-CBC successfully obscures the original content and provides strong encryption.

<img width="1600" height="921" alt="task2 lab3" src="https://github.com/user-attachments/assets/6c9674dd-3d11-4dbe-b902-78a4a9cb8038" />



**Image explanation:** The commands encrypt pic_original.bmp using AES-128-CBC, preserve its first 54 bytes, and combine the original header with the remaining encrypted bytes. The reconstructed image appears as visual noise.

**Clarification:** The shown command encrypts the entire BMP first, then replaces the beginning with the original header. It does not separately encrypt only the pixel data. Also, the visual result demonstrates that recognizable patterns are obscured in this example; it is not a complete security evaluation.

In this task, the image file pic_original.bmp was encrypted using AES in ECB (Electronic Codebook) mode with OpenSSL.
Steps performed:

1. The image was encrypted using:

openssl enc -aes-128-ecb -e -in pic_original.bmp -out new.ecb.bmp -K 00112233445566778899AABBCCDDEEFF

2. The image header (first 54 bytes) was extracted and stored in a separate file.
3. The body of the image (pixel data) was encrypted.
4. The final encrypted image was created by combining the original header with the encrypted body.

Result:
The encrypted image still partially reveals the structure of the original, showing that ECB mode does not fully hide patterns, making it less secure for image encryption.

<img width="1600" height="900" alt="task2(2) lab3" src="https://github.com/user-attachments/assets/1b74eeb5-d9fc-4b15-a389-8b9b2dd77b05" />



**Image explanation:** The image is encrypted using AES-128-ECB and reconstructed with its original header. Recognizable shapes remain visible because identical plaintext blocks produce identical ciphertext blocks under the same key.

**Clarification:** As in the CBC example, the screenshot encrypts the entire image before replacing its header. The 54-byte header assumption applies to the BMP used in this exercise; BMP pixel-data offsets can vary.

## Task 3 :

We used the same key and IV for all modes:

- Key: 1234567890abcdef1234567890abcdef
- IV: 01020304050607080102030405060708

Result:
The encrypted files were displayed using cat and xxd, and we observed that:

- Each output file (f1.bin, f2.bin, f3.bin) was completely different,
- Even though the input, key, and IV were exactly the same.

<img width="1600" height="900" alt="task3 lab3" src="https://github.com/user-attachments/assets/f9f906fb-05ba-4cf2-817c-10fca4530770" />



**Image explanation:** The screenshot creates f1.txt, f2.txt, and f3.txt containing “12345”, then shows CBC and OFB encryption commands. The later OFB commands reuse the output filenames, replacing the earlier CBC outputs.

**Clarification:** The screenshot uses lowercase -k, so the supplied text is treated as a password. OpenSSL derives a key, and the displayed Salted__ prefix indicates a salted output format. Different salts can produce different ciphertext even when the plaintext, password, and supplied IV are the same. Therefore, the differences cannot be attributed only to the encryption modes.

### 1. Encryption:

- The file in.txt was encrypted using the openssl enc command.
- The outputs were saved as f1.bin, f2.bin, and f3.bin.

### 2. Decryption:

- The same commands were used with the -d flag to decrypt the binary files.
- The decrypted outputs were saved as p1.txt, p2.txt, and p3.txt.

### 3. Key and IV:

- The same key and IV were used across all modes for consistency.

Objective:
To verify that decryption restores the original text when the correct mode, key, and IV are used.

Result:

All decrypted files (p1.txt, p2.txt, p3.txt) matched the original input, confirming the correctness of the encryption/decryption process in different AES modes.

<img width="1600" height="900" alt="task3(2) lab3" src="https://github.com/user-attachments/assets/6f868774-be50-4809-8467-0a1d918965e2" />



**Image explanation:** The screenshot shows OFB decryption commands followed by a file-size listing. The encrypted files are 21 bytes, and the input and decrypted text files are 5 bytes each.

**Clarification:** The commands use f1.txt, f2.txt, and f3.txt rather than in.txt. Equal file sizes support the expected output lengths, but do not independently prove identical contents. A byte-for-byte comparison would verify that the decrypted files match their inputs.

| Mode | File | Encrypted | Decrypted | Padding |
| --- | --- | --- | --- | --- |
| CBC | F1.txt | F1.bin | P1.txt | 11 |
| CBC | F2.txt | F2.bin | P2.txt | 6 |
| CBC | F3.txt | F3.bin | P3.txt | 10 |
| OFB | F1.txt | F1.bin | P1.txt | 0 |
| OFB | F2.txt | F2.bin | P2.txt | 0 |
| OFB | F3.txt | F3.bin | P3.txt | 0 |

**Table clarification:** AES-CBC uses 16-byte blocks. With default padding, a 5-byte input requires 11 padding bytes. The supplied screenshots show all three input files as 5 bytes, so they support 11 padding bytes for each CBC input, rather than the different values listed in the original table.

OFB does not require block padding. In the captured salted output format, the 21-byte encrypted files contain a 16-byte header and salt plus 5 bytes of ciphertext.

## Conclusion

The goal of this lab was to practice AES encryption and decryption using OpenSSL, understand the role of IVs and padding, compare ECB and CBC through image encryption, and examine how encryption modes and password-based key derivation affect ciphertext and file sizes.
