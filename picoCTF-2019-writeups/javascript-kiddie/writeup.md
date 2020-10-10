# picoCTF 2019

## Javascript-kiddie (Web exploitation)

Although, this challenge was put under the category of web exploitation, i believe a major part of the challenge revoved around forensics.\
This question consist of a link to a webpage: [link](https://2019shell1.picoctf.com/problem/26832)
 
 On opening the webpage, one can see a input element and nothing else. On entering any arbitary value to the input element, and submitting it,\
 one can see a broken image is generated. This means, the image supplied to us is corrupted and cannot be rendered. One interesting point to not here is\
 once we submit, the whole page doesn't reload, rather only the broken image is generated. This gives a hint that jquery or AJAX is being used.\
 So, checking the files supplied from server in the developer console, I found a file is being supplied as a response to a XMLhttp request.\
 On opening the file, one can find a series of numbers in the file.
 
 ---
 ```
 59 120 172 124 140 0 73 158 164 109 61 140 73 175 14 206 200 239 223 243 254 10 26 254 255 34 202 16 0 44 235 218 137 252 155 0 207 0 1 59 78 222 89 190 154 245 147 0 0 80 254 71 96 73 68 69 0 122 90 201 98 251 10 82 164 0 188 114 20 144 88 10 1 231 163 66 110 73 0 108 48 0 0 2 231 194 75 114 84 234 105 13 61 130 68 164 128 16 78 191 74 94 160 65 165 27 174 0 157 72 95 55 36 163 1 1 206 46 28 186 208 68 0 237 164 192 110 209 226 10 0 97 0 164 22 36 248 0 0 143 13 77 60 108 191 61 133 252 13 81 146 110 119 0 156 237 84 174 163 249 80 241 244 86 0 243 159 156 83 120 160 42 191 210 126 63 2 143 33 59 133 242 187 235 1 28 133 231 122 187 222 52 142 26 107 146 26 75 171 52 199 155 79 26 56 119 173 125 191 42 86 159 70 250 48 173 236 22 121 54 144 250 6 62 190 136 243 13 84 120 193 108 210 222 176 249 83 172 106 116 250 145 73 212 241 19 63 241 83 11 117 56 207 28 223 190 249 55 88 72 11 233 243 215 90 227 157 177 249 15 255 2 172 110 63 157 243 19 44 136 153 249 245 245 212 156 1 157 116 7 134 103 94 66 83 154 122 67 210 78 125 255 219 56 241 188 8 8 90 252 155 177 99 32 75 136 98 96 72 114 73 19 250 32 8 92 90 20 13 247 164 137 195 35 221 34 39 195 172 211 201 104 160 35 241 77 5 199 99 254 121 82 19 233 46 102 192 100 106 248 191 217 223 182 252 125 247 72 222 100 39 70 127 33 84 189 134 156 167 106 36 76 13 98 249 250 119 149 205 249 149 145 144 31 135 158 153 152 45 199 118 29 102 226 102 61 31 115 150 33 162 147 168 76 123 238 83 255 235 144 215 121 124 195 174 82 78 20 245 253 173 90 129 115 32 234 239 125 164 234 99 113 77 130 151 31 40 97 199 114 248 62 165 223 146 207 203 160 181 92 147 88 237 183 214 249 158 221 45 131 102 207 231 33 87 99 179 229 10 127 21 0 255 145 214 191 218 30 187 243 137 231 154 167 136 120 227 16 234 65 223 143 210 83 168 172 144 55 151 217 35 211 253 252 219 252 175 240 171 177 82 120 83 199 123 239 243 203 179 123 249 190 241 134 122 230 32 223 225 169 55 254 14 8 53 17 132 157 126 175 215 139 49 247 207 142 251 17 31 87 249 117 190 89 195 42 237 213 163 127 21 209 113 157 95 187 130 110 50 29 207 6 195 147 61 181 57 223 190 236 251 219 235 183 111 56 39 85 30 127 143 83 166 253 127 191 126 51 59 114 174 178 38 127 183 14 103 204 156 227 43 66 47 126 124 255 34 247 206 109 137 146 7 122 219 249 32 245 73 31 126 110 174 40 140 32 72 7 254 184 103 234 45 253 39 227 201 44 127 173 179 255 177 78 32 190 121 251 242 126 255 78 229 63 141 159 234 254 249 131 68 239 199 206 241 63 255 107 62 190 104 126 53 59 191 23 242 194 103 205 96 132 143 84
 ```
 ---
 
This doesn't make sense now. So, I next checked the source code of the webpage, and it had a JS script in it. 

```
<html>
	<head>    
		<script src="jquery-3.3.1.min.js"></script>
		<script>
			var bytes = [];
			$.get("bytes", function(resp) {
				bytes = Array.from(resp.split(" "), x => Number(x));
			});

			function assemble_png(u_in){
				var LEN = 16;
				var key = "0000000000000000";
				var shifter;
				if(u_in.length == LEN){
					key = u_in;
				}
				var result = [];
				for(var i = 0; i < LEN; i++){
					shifter = key.charCodeAt(i) - 48;
					for(var j = 0; j < (bytes.length / LEN); j ++){
						result[(j * LEN) + i] = bytes[(((j + shifter) * LEN) % bytes.length) + i]
					}
				}
				while(result[result.length-1] == 0){
					result = result.slice(0,result.length-1);
				}
				document.getElementById("Area").src = "data:image/png;base64," + btoa(String.fromCharCode.apply(null, new Uint8Array(result)));
				return false;
			}
		</script>
	</head>
	<body>

		<center>
			<form action="#" onsubmit="assemble_png(document.getElementById('user_in').value)">
				<input type="text" id="user_in">
				<input type="submit" value="Submit">
			</form>
			<img id="Area" src=""/>
		</center>

	</body>
</html>

```

In the form segment of the html code, on submitting the input, a function **assemble_png** is called with the input string being passed as the argument.\
The definition of the assemble_png is in the script section of the html code. \
Before ** assemble_png** function is called, a array **bytes** is declared. Then, a XMLhttp request is sent to fetch the _bytes_ file and \
and the contents of the bytes file are converted to number and are stored in the **bytes** array. Basically, the string of whitespace separated numbers is converted to a array of numbers.

Now into the **assemble_png** function, a variable **LEN** with initalized value of 16. Then a variable **key** is declared which is assigned a string "0000000000000000". The length of the string is 16. Also, the input string is assigned to the **u_in** variable. Next, the **key** variable is assigned the value of **u_in** variable only when **u_in.length == 16**. \

Now, next a array **result** is declared.

The variable **shifter** is declared which is assigned some numeric value.

```
shifter = key.charCodeAt(i) - 48.
```
So basically, the ASCII code of the character at **i**th index minus 48 is assigned to **shifter**. The substraction of 48 gives a hint that our input should be digits as 48 is the ASCII encoding code for 0. 

Now, **result** array is assigned the values from **bytes** array and then after that, zeroes at the end of the **result** array are removed until last element is not 0. Now, the **result** array is converted to png image data and then rendered on the screem. This step provides a hint that, the integers in the **bytes** array are raw **PNG** image data. But the integers in **bytes** are assigned to **result** array. So, the error in image rendering is due to the raw image data being stored in the wrong order in the **result** array. To rectify that, one has to select the values to be inserted in the input in such a way such the value of shifter leads to assignment of values to **result** array in the right order. 

This can be easily calculated knowing the first 16 bytes of a PNG file and then selecting the value of shifter such that the first 16 bytes are assigned in correct order. 

Finally, the input string is: 

```
2363911433750653
```
This leads to correct rendering of the image which is a QR code. Reading the QR code using a QR code reader will give the flag. :)
