# 1.mysql给用户添加远程访问权限


```
create user 'admin'@'%' identified with  mysql_native_password by '你的秘密';
FLUSH PRIVILEGES;
```
建议不要添加root，新创建个用户

查看已有的用户列表

```
SELECT * FROM mysql.user;

```
