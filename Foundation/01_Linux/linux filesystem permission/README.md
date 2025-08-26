$ cat app.js
$ touch feature1.js 
$ chmod +x app.js
chmod: changing permissions of 'app.js': Operation not permitted
$ su - abhijeet
Password: 
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ sudo userdel -r dev_user
[sudo] password for abhijeet: 
userdel: user dev_user is currently used by process 33596
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ rm -rf team_project/
abhijeet@abhijeet-IdeaPad-Pro-5-16IRH8:~$ 
