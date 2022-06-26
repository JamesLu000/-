# [從零開始之我要找工作] - 4 nginx -> uwsgi -> django -> postgresql 踩坑日記（1） - nginx

接下來的踩坑日記會以類似Q&A的方式進行，主要是為了避免未來重新安裝或搭建的時候重複再次跌入同樣的坑，然後還花大把的時間去重新研究。

nginx -> uwsgi -> django 搭建可以參考[DevOps：如何將 Nginx + uWSGI + Django 的服務部署在一台主機的根目錄][]。

## 問題1：nginx -> uwsgi -> django 搭建完畢之後發生了 HTTP 400 bad request。

可以參考ref1的內容。主要原因是 ```
/usr/local/etc/nginx/sites-available/```
中的config

```python
server {
	listen       80;
	server_name  _;
	...
}
```

server_name  _;
本身無法與任何的request進行配對，導致實際上沒有任何的request可以成功進入這個block，因此比如reverse proxy等功能根本無法被使用。詳細可以參考[我所理解的 nginx 中 server_name "" 与 server_name _ 之间的区别]。

解決方法：把 ```server_name  _;```
換成 ```server_name  yourDomain.com;```

```python
server {
	listen       80;
	server_name  yourDomain.com;
	...
}  
```

## 問題2：把domain掛上cloudflare代管之後，cloudflare給予的SSL certificate與let's encrypt方案差異

可以參考cloudflare官方提供建議的digitalocean提供的教學，如[How To Host a Website Using Cloudflare and Nginx on Ubuntu 20.04]。

但是重點在於本SSL的方法與一般使用let's encrypt的方法不同，本方法可以被理解為只有cloudflare的server到Web Server這兩「端點」之間有SSL，如果其他來源的連線則無法使用SSL。比如：以 ```https://serverIP``` 連線是無法啟用SSL的。另外，如果使用此方法進行連線，還需要注意nginx config當中server block的設定（前面server_name的設定）。

小tips：目前cloudflare免費方案的reverses proxy所支援的port是以常見的80以及443為主，所以使用 ```https://yourDomain``` 進行連線的話需要於Server端與client端考慮port等問題。另外，如果於cloudflare的dashboard設定僅DNS，則雖然依然能以HTTPS連線，但是瀏覽器會跳安全性風險（證書的簽發沒有受到可信的認證），而如果經過cloudflare，則我們的證書有受到cloudflare的認證。

[DevOps：如何將 Nginx + uWSGI + Django 的服務部署在一台主機的根目錄]:https://orcahmlee.github.io/devops/nginx-uwsgi-django-root/

[我所理解的 nginx 中 server_name "" 与 server_name _ 之间的区别]:https://nicechiblog.com/article/22/article_1599041059396.html

[How To Host a Website Using Cloudflare and Nginx on Ubuntu 20.04]:https://www.digitalocean.com/community/tutorials/how-to-host-a-website-using-cloudflare-and-nginx-on-ubuntu-20-04




