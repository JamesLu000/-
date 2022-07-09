# [從零開始之我要找工作] - 7 Django的概論速通（1） Django的基本架構以及如何完成一次基本的HTTP Request

Django是一個後端框架，主要的架構是以MVT為核心，該框架的特點是相較於一些「輕量」的框架而言是相對厚重的，提供了很多豐富的內建函式庫。

除此之外，本專案使用 rest framework這個「框架的框架」則是特別擅長用來建立restful api，他提供了一些更高層（更抽象）的物件。因此 rest framework的搭配使用可以讓Django的後端開發速度加速，或者提供一些更簡潔的寫法。比如 rest framework 提供的 Response 即可自動化的返回 client 端要求的返回格式，不需要開發者進行額外的格式判斷。

## 所謂的MVT與主要架構
如果有需要對Django框架本身進行了解，可以參考 [Mozilla Django 教學] 並嘗試一步步跟著搭建。

首先最開始的時候需要先安裝好 Python 以及 Django 接著執行：

```python
django-admin startproject mytestsite
cd mytestsite
python3 manage.py startapp myapp
```

然後你就有了 mytestsite 這個 project ，接著所有的操作都在這個 mytetsite 裡面執行。

manage.py 

顧名思義就是這個 project 的管理程式，無論 makemigrations （建立資料庫遷移）、 migrate （將遷移寫入資料庫或者資料庫設定修改）、 runserver （啟動網頁伺服器）...等都是對這個檔案執行。

mytestsite/setting.py

顧名思義則是 mytestsite 這個 project 的設定檔案，其中定義了諸如啟用哪些 application 、 template 的根目錄、static 的根目錄、資料庫管理…等等。

新增的 project 以及 application 均依照下方的MVT 架構進行開發，盡量不要去更改該架構。

在此提供一張 Mozilla 提供的 MVT 架構圖 ![MVT 架構圖](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Introduction/basic-django.png)

簡言之，一個HTTP Request進入之後會通過mytestsite/urls.py 上面寫的「路由表」進行routing，前往各個不同的 application 或者不同的 views.py。

各個 views.py 裡面提供了收到 HTTP Request 之後的種種運算，比如收到 「```GET yourweb.site```」之後要返回何種結果之類的，前述的 rest framework 這個框架就是使用在這裡。

各個 models.py 則是定義了各個 application 的資料庫規則，比如在「圖書管理 application」當中定義一個 book table 與 user table兩者的關聯（book 有一個欄位是 user 有 多對多、 foreign key 的關聯等等）

最後 Template 則是通常會被放在一個透過 setting.py 定義的 template 資料夾內的html檔案，該檔案「們」除了傳統的 html tag 之外，透過``` {% %} ```與``` {{ }} ```的方式來支援類似 Python 的 Django-html 語法。

## 當一個 HTTP Request 進來之後

當 Server 收到一個 HTTP Request 之後，會進入 mytestsite/urls.py 當中的 ```urlpatterns = [ ... ]``` 進行 routing。

可以考慮參考 [rest framework router 官方文件] 或者單純只使用 Django 內建的 ```path("",include("yourAPPurl.py"))``` 

或者

```path("",yourAPIview.as_view())```

routing 結束之後會進入各個 views.py 進行邏輯運算，以及連接資料庫進行資料的存取與讀寫。

最後也是通過 views.py 拿取特定 template.html 進行 render 後返回，或者返回 json 等特定格式給 client ，以上即完成一次 Django server 的回應。

## 一個簡單的 views.py 範例

```python
def hello(request):
    user = request.user
    return render(request, "login.html", locals())
```
在此提供一個簡單的 views.py 的範例。收到HTTP Request 之後 urls.py 會將其 routing 至 views.py。以範例來說就是 ```hello()``` 會收到 Request，接著提取 Request 當中的 user 並儲存成 user 這個變數。

接著返回一個完成渲染的 html 頁面，該頁面以 ```login.html``` 為 template ，```locals()``` 則傳 user 以及其他於 ```hello()``` 當中定義的變數，使該頁面的 Django-html 語法可以直接呼叫 user 等變數。

[Mozilla Django 教學]:https://developer.mozilla.org/zh-TW/docs/Learn/Server-side/Django
[rest framework router 官方文件]:https://www.django-rest-framework.org/api-guide/routers/
