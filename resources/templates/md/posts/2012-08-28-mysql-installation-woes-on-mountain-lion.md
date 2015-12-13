{:title "MySQL installation woes on Mountain Lion"
:layout :post
:tags ["mysql" "tools"]}

After doing a fresh installation with Mountain Lion, I've had a hell of a time properly install
MySQL. I've had to use a few sources to figure this one out, and here are the steps I needed to
take.

I'm not 100% sure that [these steps][1] are needed, but they couldn't hurt.

```
sudo chown -R `whoami` /usr/local
brew update
```

Some background.

```
brew install mysql
sudo mysql_install_db --user=mysql --tmpdir=/tmp --basedir=/usr/local
mysql.server start
```

Now test with

```
mysql -uroot
```

[1]: http://robots.thoughtbot.com/post/27985816073/the-hitchhikers-guide-to-riding-a-mountain-lion
