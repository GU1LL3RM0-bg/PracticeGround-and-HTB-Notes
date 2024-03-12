
#### Initial Access - .htaccess to get RCE

1. Upload an htaccess file that allows an specific extension to be run. In this case, we can add our own .evil extension and tell the server to accept it as php code
```sh
cat .htaccess        
AddType application/x-httpd-php .evil
```
2. Upload the .htaccess, usually programmers filter a lot of php extensions but they forgot to filter for .htaccess files which can be abused to define new allowed extensions.
3. Upload a php reverse shell with `.evil` extension.
4. Run the evil file and catch the reverse shell with nc

![[Pasted image 20231120221159.png]]

![[Pasted image 20231120221152.png]]

