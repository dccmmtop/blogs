### 修改postgres的登录密码

1. open the file `pg_hba.conf` for Ubuntu it will be in `/etc/postgresql/9.x/main` and change this line:
> `local   all             postgres                                peer`
>
> to
>
> `local   all             postgres                                trust`

2. restart the server

> ```
> sudo service postgresql restart
> ```

3. Login into psql and set your password

   > `psql -U postgres`
   >
   > `ALTER USER postgres with password 'your-password'`



4. Finally change the `pg_hba.conf` 

   > `local   all             postgres                                trust`
   >
   > to
   >
   > `local   all             postgres                                md5`to

After restarting the postgresql server, you can access it with your own password