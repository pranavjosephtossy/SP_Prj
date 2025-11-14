# Known
## Stored XSS
The site allows raw input for both username and comments. A malicious actor could add in code, for example a script tag, as either the username or cooment. What this would do is when the comment is "published", another user who opens the comments section would "execute" this line of code, basically carrying out the code left by the attacker. 
![exploit working](pics/XSS/XSSsto1.png)


This is where the vulnerability in the code was spotted:
![vulnerable code](pics/XSS/XSSsto2.png)
In this project the vulnerability could be exploited by a malicious user leaving a comment. This could happen either by adding the alert script in username field or as the actual comment. The site does not at present filter/sanitize user input. The {{ text.username | safe}} and {{ text.comment | safe}} means it is safe to "render" whatever is passed on. 



This is the input form presented to the user:
![input form](pics/XSS/XSSsto3.png)
They attacker the adds the following script: `<script>alert('xss')</script>` intp the input forms

This is solved by either sanitizing the user input or using the {{text.comment }} without |safe, which prevents the comment from being rendered as the intended.
![fixed code](pics/XSS/XSSsto4.png)


![exploit no longer working](pics/XSS/XSSsto5.png)




## Reflected XSS
The site allows for raw input into the search bar. This means a malicious actor could misuse the search function by inputting malicious code. They could gain unnecessary information like information regrading the server and use it later in a sophisticated attack.
![Screenshot](pics/XSS/XSSref1.png)


This is where the vulnerability in the code was spotted:
![Screenshot](pics/XSS/XSSref2.png)
In this project the vulnerability is exploited by the user using the searchbar. This attack can take place by "searching" a malicious line of code. The reason this works is because one again the site does not filter/sanitize user input. The `{{ query | safe}}` means it is safe to "render" whatever is passed on.
They attacker the adds the following script: `<script>alert('xss')</script>` into the input form which causes the attack.


This is solved by either sanitizing the user input or using the `{{ query }}` without |safe, which prevents the comment from being rendered as the intended.
![Screenshot](pics/XSS/XSSref3.png)


## Path Traversal
This site allowed the movement/traveral from one directory to another. This is a big vulnerabilty because a malicious user to navigate to beyond what the user is supposed to see. Malicious actors could traverse directories like this and download restricted files. Granted while they wouldnt known the exact structure, they could still try multiple possibilites, taking advantage of comman structures and directory names.
![Screenshot](pics/pathtrav/pathtrav1.png)

This is where the vulnerability in the code was spotted:
![Screenshot](pics/pathtrav/pathtrav2.png)
In this project, malicious actor can input into the search bar of the browser, as a suffix the following `/download?file=../trump.db`. As seen in the first screenshot, this enables the download of the entire database which is a major breach. This is becose `.abspath()` converts a path into an absolute path without checking if it stays inside the directory

 This was fixed by using `os.path.normpath` which ensures the path starts with the base directory preventing traversel.
![Screenshot](pics/pathtrav/pathtrav3.png)

The exploit no longer works:
![Screenshot](pics/pathtrav/pathtrav4.png)

# Unknown
## Cryptographic Failures
While fixing the path traversal vulnerability was able to prevent download of .db, if the db was ever leaked, plaintext passwords of users would be leaked. This could be used by malicious users to used said leaked password on other account belonging to the user in the hope that the user reuses passwords.
![Screenshot](pics/unknown/cf1.png)

The password was not stored in a secure format in the db. It was not hashed. As per the lastes OWSP top 10 list, cryptographic failures were listed as the 2 hishest risk. This means if the db was compromised an attacker would have all the user passwords and it could be interpreted imediately. This is where the vulnerability in the code was spotted:
![Screenshot](pics/unknown/cf2.png)

This was fixed by first hashing all the previous passwords and comparing the hashs of the user password and the hash of the user input. A function to carry out previously entered unhashed passwords was made and "called" evrytime at the "start":
![Screenshot](pics/unknown/cf3.png)

The `hash_current_passwords()` functions connects to the db and hashs all the passwords. It is called inside:
![Screenshot](pics/unknown/cfa.png)
immediately after the db is created

And an updated login to compare hashs and not decrypted passwords:
![Screenshot](pics/unknown/cf4.png)


Heres what the db would look like if the hacker still managed to access it:
![Screenshot](pics/unknown/cryptfail.png)
Even if they are able to obtain the hashs its harder and more time consuming than having access to the plaintext password