# PicoCTF 2019

## What-lies-within (Forensics) (steganography)

Uses the concept of LSB encoding. Each bit in the binary form of the flag is stored in the the LSB of each pixel colour number (which ranges from 0 - 255 for grayscale images). This method is otherwise known as LSB encoding. One can use **zsteg** tool to find the flag. 
