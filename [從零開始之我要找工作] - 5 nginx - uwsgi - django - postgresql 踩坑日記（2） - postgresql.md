# [從零開始之我要找工作] - 5 nginx -> uwsgi -> django -> postgresql 踩坑日記（2） - postgresql 

本踩坑日記由於django的部份邊開發邊踩，所以打算等開發到一定程度再寫，當然也有可能不是踩坑日記而是一般的開發日常。另外uwsgi的部份則是沒有遇到太多問題因此直接跳過，所以來到了postgresql的部份。

postgresql的部署可以參考digitalocean上的這篇文章[How To Use PostgreSQL with your Django Application on Ubuntu 20.04]

## 問題1：django -> postgresql 搭建完畢之後於query時出現 "relation does not exist"

可以參考[【error】postgresql relation does not exist]這篇文章的說法。簡言之，由於django內建的ORM物件於「翻譯」成SQL語言時大小寫的處理邏輯沒弄好，因此當django當中app的名稱大小寫混寫，比如使用camel style的時候就會出事。所以再django當中app的名稱以小寫為宜。

由於django支援使用ORM進行存取（本專案也這樣用），簡單來說就是把撰寫SQL query的工作包成一個又一個的Python物件（詳見django.db.model），而後端使用者透過這些物件提供的各種方法進行操作，這樣的方法讓開發者不需要懂SQL語法，只要「寫邏輯」就可以了，缺點就是如上，除非進去改ORM的SQL語法生成器，否則就要按照官方的建議走（當然，官方的建議資料庫是SQLite，postgresql的支援度不夠完美也是可以理解的）。

## 問題2：萬一不小心踩了上面的坑怎麼辦？

如果已經不幸使用django的migrate遷移入新資料庫了，建議可以直接砍掉該app專案，接著把已經寫完的部份搬到新命名的app資料夾中。

**<font color="#f00">「絕對不建議使用改名工具或者單純改檔名」</font>**

我沒有深入研究，但是根據實際使用的經驗是，該app已經寫入到django其他的管理部件的相依性當中了，因此一旦「只是改名」的話只會導致原本完好的部份因為相依性缺損而整個django都無法成功啟動。

[【error】postgresql relation does not exist]:https://blog.csdn.net/tanzuozhev/article/details/78273558

[How To Use PostgreSQL with your Django Application on Ubuntu 20.04]:https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-your-django-application-on-ubuntu-20-04
