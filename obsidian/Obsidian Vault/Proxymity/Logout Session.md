- Installed Burp
![[Pasted image 20241112103538.png]]
- Logged in to a (local, run with npm start) client app in the Burp browser
- Intercepted a request and moved it to the Burp Repeater:
![[Pasted image 20241112103620.png]]
- Logged out
![[Pasted image 20241112103757.png]]
- Used Repeater to send the captured request again after ~ 5 min:
![[Pasted image 20241112104547.png]]
* The response is 200 even though we logged out


Revoked refresh token 13:02
