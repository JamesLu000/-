# [從零開始之我要找工作] - 3 Oracle雲端VM踩坑日記之防火牆問題

話說從頭，由於Oracle雲端非常大方的給4核24G ram的arm A1 VM永久免費試用，因此當然是把Web Server搬到雲端上面去啦～

來介紹一下目前的開發狀態，目前我的Domain是使用freenom申請免費.ga結尾的Domain，接著掛在cloudflare上面做DNS代管，順便用他們來產生SSL憑證。另外OS是不舊不新的Ubuntu 20.04 LTS。

目前遇到的神奇問題是，隨著Django的學習後，總是想著還是希望可以把網站實際的掛上public ip跟Domain，另外也可以在Local Coding的電腦上面可以直接開啟瀏覽器進行瀏覽（目前是用VScode的remote功能連進去進行開發）。

結果就開始遇到一連串的問題，也就有了這個障礙排除的學習系列。

## 外部的public ip無法連線到Web Server去

如題，遇到這種狀況，100%一定是防火牆問題呀，不外乎供應商不然就是OS的防火牆把port擋住了。

### 想當然，一定是檢查防火牆啦～這麼基本對吧！

呵呵呵。

我當然也是這樣想的呀。所以秉持著試用過多家雲端的經驗，那就是在cloud的管理頁面中找到了如圖的設定之後快速的一路設定下去，同時也把ufw與iptables這兩個防火牆service全部關掉了。應該也就一帆風順了吧？這個… 就是Oracle跟其他雲端商的差距所在了…。

![OCI設定]（https://raw.githubusercontent.com/JamesLu000/REZeroRequireJobs/main/img/OCI設定.png ）

![OCI設定2]（https://raw.githubusercontent.com/JamesLu000/REZeroRequireJobs/main/img/OCI設定2.png ）

後來當然還是不行囉。怎麼會讓你這麼簡單就能動呢，如果是這樣，就不會有這篇文章了…。

崩潰之餘，只好努力google囉，本來預期應該是Oracle的設定有什麼特別的，所以花了不少時間爬Oracle相關的網路架構與設定等等。當然，每個文章與教學的答案都是上圖那樣的設定方式…

### 解決方法

沒有想法之下，只好轉往試試看設定一下OS裡面的防火牆，結果…，下service iptables status吐出inactive的iptables居然持續在作用中（如圖）！！！，另外還不能直接設定所有TCP流量全開…。

![iptables.service]（https://raw.githubusercontent.com/JamesLu000/REZeroRequireJobs/main/img/iptables.service.png ）

所以只好把iptables跟ufw兩個services掛回來，接著乖乖設定好http跟https的ports。噹噹，成功了…，居然基於一些迷之原因，被停掉的服務還能持續產生影響，不能用把防火牆關閉就當作沒有防火牆。

## 懶人包與筆記

可能是因為OS為Oracle修改過的版本，OS的防火牆似乎禁止被關閉（或者關閉無效），因此… 如果使用Oracle OCI的話，不能貪圖方便關掉OS內部的防火牆，請好好的設定port的部份…。

可以嘗試參考 [Can't access Oracle Cloud Always Free Compute http port] 以及 [Opening port 80 on Oracle Cloud Infrastructure Compute node] 這兩篇文章貼文。

[Can't access Oracle Cloud Always Free Compute http port]:https://stackoverflow.com/questions/62326988/cant-access-oracle-cloud-always-free-compute-http-port

[Opening port 80 on Oracle Cloud Infrastructure Compute node]:https://stackoverflow.com/questions/54794217/opening-port-80-on-oracle-cloud-infrastructure-compute-node