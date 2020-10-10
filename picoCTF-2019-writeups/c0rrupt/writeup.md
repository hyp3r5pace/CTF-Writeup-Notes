# picoCTF 2019

## cOrrupt (Forensics)

This is a good challenge. This challenge took me a good amount of time to figure out. This challenge provided a file, which didn't show a extension in the name of the file. Also, it can be sometimes be misleading and being a forensic challenge, I was expecting that. But the challenge setter didn't do that. Next, applying **file** bash command on the downloaded file identifies the file as data file, which is of not much help :/ \
Next, step was to obviously check for the file signature and the structure of the file to figure out what type of file it is. So, I viewed the raw form of the file in _hexedit_. Soon, i checked for the file signature, and it was en4, which is a valid file signature. But something was off. The other part of the file was not complying to a file with signature en4. As I scrolled to the end of the file, the file had 4 bytes whose ASCII representation was **IEND**. And bingo, I realised, it is most probably a PNG file. Also, I checked for other chunks type but couldn't find. But as the challenge name is corrupted, i realised the file is corrupted and I need to fix it. Then, I changed the file signature to PNG and also checked for other possible essential chunks in PNG, and soon found some 4 letter strings which closely resembled to essential chunks **IHDR** and **IDAT**. So, I changed those chunk types to appropiate chunk names. Then I saved the changes and tried to open the file, but the image editor couldn't open the image file. It said the file is corrupted. So, I ran a **pngcheck** command to figure out what is the error in the PNG file.\
**Pngcheck** mentioned the IDAT chunk data size is quite big but I was quite confused by that. So I read some more about png file structure and realised and fiddled here and there, and realised the actual IDAT chunk data is not equal to size mentioned in the size field of the IDAT chunk. So, I corrected the chunk size field and then again did a pngcheck and it showed some unmatched **CRC** in some ancilliary chunk, which I corrected. Then I again ran pngcheck and showed everything is ok :)
Then I opened the png file in a image viewer and voila, the image showed the flag.

### FLAG
---
`
	picoCTF{c0rrupt10n_1847995}
`
---
