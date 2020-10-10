# picoCTF 2019

Good question. This question required good understanding of txt files, character encoding (UTF-8 and ascii) and pattern recognition.

### My approach to the solution

1. First, being a forensics question, I tried to do is run file command on the txt file i recieved, and it gave an output of **whitepages.txt: UTF-8 Unicode text, with very long lines, no line terminator**. This gives an idea that the whole text file is most probably a single line. A single line is considered as a long line when the length of the line is greater than 300 characters. The no line terminator means, the line doesn't have a new line character in the end. This is a standard considered in unix systems.

> A string is considered to be a new line if it ends with a new line character in the end. Even the last line of a text file should end with newline character.

Also, it is evident that the characters in the text file are encoded in **unicode UTF-8**.

Simply opening the file will give only a blank file as the text file only contained blank spaces. Now, the blank spaces are here of two types. unicode UTF-8 contains blanks spaces of different codes other than just tabs and spaces as in ASCII. The one used here are _0xe2 0x80 0x83_ (EM SPACE) and _0x20_ (NORMAL ASCII SPACE character).

the hexdump of the text file is:
---

> 00000000  e2 80 83 e2 80 83 e2 80  83 e2 80 83 20 e2 80 83\
> 00000010  20 e2 80 83 e2 80 83 e2  80 83 e2 80 83 e2 80 83

---

After this, it is obvious that only two characters existed in the text file, one is EM SPACE (_0xe2 0x80 0x83_) and ASCII SPACE (_0x20_). It can be infered that the spaces can be considered as binary of 1 and 0 and that string of binary in ASCII may contain the flag. To convert the following to a binary string, I wrote a C++ program and it gave me the binary string. Then I passed the binary string through a binary to ASCII converter and it gave the string.

The CPP program

---

```
#include <bits/stdc++.h>
#include <fstream>

using namespace std;

int main() {
    std::ifstream file;
    std::string line;
    file.open("whitepages.txt", ios::in);
    if (!file) {
        cout << "Error in file opening!\n";
        return -1;
    }

    getline(file, line);

    cout<< line << line.length();

    vector <int> bits;

    for (int i = 0; i < line.length(); i++) {
        if (((unsigned) line[i]) == 32) {
            bits.push_back(1);
        } else {
            bits.push_back(0);
            i+=2;
        }
    }

    cout<<"\n";

    for(int i = 0; i < bits.size(); i++) {
        cout<<bits[i];
    }

    file.close();
    return 0;
}

```

---

After converting the binary to ASCII, the flag was achieved:
---

`

		picoCTF

		SEE PUBLIC RECORDS & BACKGROUND REPORT
		5000 Forbes Ave, Pittsburgh, PA 15213
		picoCTF{not_all_spaces_are_created_equal_dd5c2e2f77f89f3051c82bfee7d996ef}
`

---
