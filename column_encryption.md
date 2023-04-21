# Encrypting a column 

```
# Encrypt 
drop schema if exists crypttest99
create schema crypttest99;
use crypttest99;

-- attention: binary does not work, because of padding 
-- https://stackoverflow.com/questions/16224515/why-is-aes-decrypt-returning-null
-- Because if AES_DECRYPT() detects invalid data or incorrect padding, it will return NULL.

create table cryptic(id int, title varbinary(20);
insert into cryptic(id, title) values (1, AES_ENCRYPT('mysecrettext',SHA2('password-key',512))) 


```

```
# Decrypt 
select id,aes_decrypt(title,'password-key')  from cryptic;

```

## Reference:

 * https://mariadb.com/kb/en/aes_encrypt/
 * https://mariadb.com/kb/en/aes_decrypt/
