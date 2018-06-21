---
layout: post
title: 自己做 Python 的 With Block
tags:
image: +programming.png
comments: true
---

with 在 Python 是個很神奇的功能。他看似只在幫你開檔案的時候幫你，也不知道真正做了什麼，但是所有人都叫你開檔案的時候包一下。其實它的用處還有很多有趣的作法呢 :)

其實 with 他是個在 python 的 code block 前後幫你偷偷做事情的東西，在經典的開檔案例子中，他就會幫你偷偷在最後把檔案關掉，以免你的 file descripter 用太多個。

```python
with open("file.txt") as f:
    print(f.read())
    # don't need to close f	
```

但是這個應用實在太無聊哈哈，他既然可以幫你關檔案，當然可以幫你把更多東西藏起來。下面我們用個 API Server 當做例子。我們通常可能會需要在處理完邏輯之後做一個回應的動作(你語言癌嗎)。

```python
try:
    # do something
    if success:
        status_code = 200
        rtMsg = "We are good!"
    else:
        status_code = 400
        rtMsg = "Are you ok??"
except:
    status_code = 500
    rtMsg = "Oooops, internal error"

send(status_code, rtMsg)

```

這樣的寫法會讓回應的程式和主要的邏輯混在一起。有些 status code回應的訊息其實非常罐頭，並不需要穿插在主要邏輯裡面使得程式可讀性變得很差，也讓人很煩躁。

好在，在 Python 中有個內建的函式庫叫做 [`contextlib`][https://docs.python.org/3/library/contextlib.html]可以幫你做出自己的 with block ，把這些東西都打包起來。

首先，讓我們先定義一個處理各種回應的髒東西的 helper class。

```python
class resultHandler(object):
    def __init__(self):
        # might have some other params
        self.msg = ""
        self.status_code = 200
        self.rtMsg = ""

    def success(self, msg):
        self.msg = "Success"
        self.rtMsg = msg

    def fail(self, type, msg):
        self.msg = "Failed"
        self.status_code = type
        self.rtMsg = msg
    
    def teapot(self):
        self.msg = "Dunno"
        self.status_code = 418
        self.rtMsg = "What do you want from a teapot?!"

    def intError(self, e):
        self.msg = "Internal Error"
        self.status_code = 500
        self.rtMsg = str(e)
    
    def _send(self):
        # do actual returning
        print(self.status_code, self.msg, self.rtMsg)
```

這樣我們可以把很多髒東西都打包起來，如果有需要有更複雜或是有些常用的狀況類似 400 找不到東西或是 418 我是一個茶壺(誰會常用這東西拉)，都可以把它變成一個 method。

再來，我們就要來做自己的 with block 了。

```python
from contextlib import contextmanager
@contextmanager
def apiWrapper(**some_param):
    # pre-checkings, like authentication
    do_checking(**some_param)
    control = resultHandler()
    try:
        yield control
    except Exception as e:
        control.intError(e)
    finally:
        control._send()
```

透過 `@contextmanger` 這個函式裝飾，我們可以讓他變成一個可以是包含外部定義內容的函式。在 `contextlib` 中還有可以嚴格定義成物件的做法，有興趣可以去官方文件看看，他們有附上精美的例子 :) 。在這裡，使用 `yield` 就是執行外部定義的程式，後面接的變數就是可以送到外部去的變數。在這裡我們把控制回應狀態的 `control` 變數送過去，讓主要邏輯可以控制回應的內容。

而在前後我們可以做些常規的動作，類似在前面做權限的確認，或是取得公用的變數等等。我們也可以把 try except 打包在這裡面，500 Internal Error 就可以自動被處理，也可以依照是否是 debug mode 來決定要輸出多細部的 Python Exception ，甚至也可以偷偷做 logging 。

最後讓我們看看外面怎麼使用：

```python
with apiWrapper() as ctl:
    print("do things")
    ctl.success("good")

# Output:
# >> I'm checking things :))
# >> do things
# >> 200 Success good
```

像是這樣，就可以只處理主要邏輯，而其他髒髒的錯誤處理或是繁複的 http status return 等等都可以交給 `resultHandler` 來處理。這樣可以留給主要邏輯足夠的彈性，也可以把髒的東西打包起來。

讓我們看看如果有錯的話會發生什麼事情：

```python
with apiWrapper() as ctl:
    print(aaaaa)
    ctl.success("good")

# Output:
# >> I'm checking things :))
# >> 500 Internal Error name 'aaaaa' is not defined
```

如果們所願的，他會自動幫我們 try excpet 起來，並提供相對應的錯誤資訊。

當然，這不只是可以這麼使用。常用的用法也可以是 stdout/stderr 的重導向。只要是有很多區塊是需要有前置作業、後期處理，都可以用 with 打包起來讓程式看起來更乾淨嚕 :)

*剛好好朋友問我，之前在做 cython 的 stdout/stderr 重導向時看到這東西，發現其實非常好用。順手寫了個範例，想說就這樣只給他有點太浪費了，就拿來寫篇部落格啦ＸＤ 但是之前打了兩千字的東西並不是這篇，希望那篇可以早日完成不要難產了哈哈。如果正巧問我程式，我也正巧有空回答，我又很巧地寫了範例，那記得提醒我拿來發部落格哈哈哈～*