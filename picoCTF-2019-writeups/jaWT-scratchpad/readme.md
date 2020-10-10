# PicoCTF 2019

## jaWT scratchpad  (Web exploitation)

This is a good challenge. Main components of the challenge was JWT (JSON web token). To solve the challenge, one must know what a JWT is and what it does, how it is configured and it's various configuration. Main focus of this challenge was on jwt signatures. They are encrypted using HMAC-256 algorithm which uses a secret. I was able to figure out the idea that I had to change the JWT token to gain access to the flag and I did that using **jwt.io** debugger but I didn't realize, I needed to fid the secret too, otherwise by just changing the claim, I was making the token invalid as the signature was still the same and debugger couldn't change it without the secret. One main, hint which the challenge gave was to use John the ripper, link to which was provided in the webpage. One has to use john the ripper to break the HMAC-256 encryption using rockyou.txt password list and get the secret. 
It is a good challenge, learnt a lot about JWT.

Another important point to note here is that JWT is placed in cookies and here, the cookie was session based. So, if I logged out of the session, the cookie was destroyed.
