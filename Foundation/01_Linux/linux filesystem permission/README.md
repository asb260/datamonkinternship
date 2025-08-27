abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ sudo useradd -m dev_user
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ sudo passwd dev_user
New password: 
Retype new password: 
passwd: password updated successfully
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ mkdir team_project
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ touch team_project/README.md
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ echo "Team Project Plan" > team_project/README.md
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ mkdir team_project/src
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ touch team_project/src/app.js
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ sudo chown -R abhijeet:dev_user team_project/
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ ls -l team_project/
total 8
-rw-rw-r-- 1 abhijeet dev_user   18 Aug 27 12:59 README.md
drwxrwxr-x 2 abhijeet dev_user 4096 Aug 27 12:59 src
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ chmod -R 770 team_project/
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ ls -l team_project/
total 8
-rwxrwx--- 1 abhijeet dev_user   18 Aug 27 12:59 README.md
drwxrwx--- 2 abhijeet dev_user 4096 Aug 27 12:59 src
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ touch team_project/src/test.js
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ rm team_project/src/test.js
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ su - dev_user
Password: 
$ cd /home/abhijeet/team_project/src/
$ cat app.js
$ touch feature.js
$ chmod +x app.js
chmod: changing permissions of 'app.js': Operation not permitted
$ exit
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ sudo userdel -r dev_user
userdel: dev_user mail spool (/var/mail/dev_user) not found
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ rm -rf team_project/
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ 
