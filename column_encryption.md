# Encrypting a column 

```
# Encrypt 
create schema crypttest;
use crypttest;
create table cryptic(id int, title varchar(130);
insert into cryptic(id, title) values (1, AES_ENCRYPT('mysecrettext',SHA2('password-key',512))) 


```

```
# Decrypt 

```

## Reference:

 * https://mariadb.com/kb/en/aes_encrypt/
 * https://mariadb.com/kb/en/aes_decrypt/
