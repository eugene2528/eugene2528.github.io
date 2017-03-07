---
layout: post
title: 簡單的資料結構(1) - LRU
tags: []
image: +programming.png
comments: true
---

好的這是個有趣的開始，雖然好友大維希望我第一篇貼文寫 Keyword Extraction ，但是這對我來說實在太困難了呵呵。因此我打算偷懶寫個簡單的東西。這篇的主題來自於我最近又再度爆發的土炮精神，為了要讓處理資料快速又不需要把他送到資料庫，我自己寫了一個簡單的 query engine 。雖然說簡單，但是需要支援的功能也是不少所以也稍微研究一下到底要怎麼樣才能讓他跑比較快。過程當中使用了兩個簡單經典的 data structure / algorithm：LRU 及 perfect hashing。

因為我實在很懶得關係，後者下次再寫好了，今天先寫超級簡單的前者，當作經典賽前不想做事消遣(還好老闆看不懂中文)。

### Least Recently Used (LRU)

這是個在做快取 (cache) 時非常經典的演算法。

主要問題的背景是這樣的：今天你有 n 個資源請求，最好的狀態就是你的記憶體裡面剛好有要請求的資源，這樣完全不需要付出成本。當然，記憶體要錢(倒，所以通常你只會有 m 個位置可以來存東西。如果運氣不好每次請求的資源都不在記憶體裡面你就ＧＧ要付出成本來撈資源。最好的狀態就是每次請求的資源你都神一般的預測放在記憶體裡面等他來要。這需要好好的設計哪些資源要丟掉哪些資源要留在記憶體裡面，甚至預先載入等等。

**所以，這個演算法的問題就是：設計一個替換資源的方法 (replacement policy) 使得被索取資源不在記憶體裡面的次數最少 (minimize miss count) 。**

學理上，在是個 streaming algorithm 或 online algorithm 的經典問題，因為你必須要在資源請求的當下做出抽換的決定，不像一般演算法問題可以把 input 通通看光光再來做決定。這種演算法通常我們會用 competitive analysis 來看它和偷看完所有 input 才做決定的演算法 (optimal policy) 成效差多少。

簡單來說，如果我們說一個 online algorithm 是 k-competitive ，代表他平均而言的成本或是 miss rate 是 optimal policy 的 k 倍(有時候也會用效能看分析的人從什麼角度)。*學術上大家對於誰是誰的 k 倍還沒有很一定的用法，但是在文章前後文中就會很清楚知道他在講哪個了。*

講了這麼多，重要的演算法啥都沒說(被揍，應該大家早就看不到這行把分頁關掉了呵呵。

LRU 的做法非常簡單：對於現在所有已經在記憶體裡面的資源，記錄他們最近一次被用到的時間。當要選擇丟掉誰的時候，選取最近使用時間距離當下最遠的那一個。

來上個圖比較好理解，這是從[維基百科](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU)借來的圖。括號內的數字就是資源請求的時間，而前面的字母代表資源的代號。

![LRU Example](https://upload.wikimedia.org/wikipedia/commons/8/88/Lruexample.png)

當然，上述的作法牽扯找最大值等等，如果沒有好好規劃會讓這個動作非常的慢 ( 理論上最糟會到$O(n)$ )。因此通常的做法是使用一個 stack 來處理。

**每次請求資源時，如果這個資源已經在記憶體裡面，我們把它從 stack 中拿出來，然後再 push 到 stack 的最前面。如果不在記憶體裡面，如果記憶體還夠，就直接把資源撈出來放進去記憶體。如果記憶體不夠了，把 stack 最後面的資源丟掉(這樣就有位子了)，然後把現在請求的資源撈出來放進記憶體。**

假設記憶體有 m 個位子，這個做法是 m-competitive。詳細的學理證明就不寫在這裡了，如果有興趣可以再找我，我可以把演算法筆記拍給你(狀態顯示為超級懶)。

因為剛好有實作他，所以附上調整過後比較易懂(才沒有)的 code 。這是個手作 group by 的一部份，`string` 代表要寫入的內容，Id 是要分類的 ID 。因為資料龐大沒辦法直接在記憶體裡面操作所有的資料，所以採用這種迂迴的方式處理以免記憶體被我搞爆。

```python
fnStack = []
fdDict = {}
all_fns = set()
max_file_count = 250

def write(Id, string):
	fn = "./temp/grouping-{}.temp".format( hash(Id)%500 )
	if fn in fnStack:
		fnStack.remove(fn)

	if len(fnStack) > max_file_count:
		f = fnStack.pop()
		fdDict[f]["fd"].write( "".join(fdDict[f]["cache"]) )
		fdDict[f]["fd"].close()
		del fdDict[f]

	if fn not in fdDict:
		all_fns.add( fn )
		fdDict[ fn ] = { "fd": open(fn, "a"), "cache": [] }
	
	fnStack.insert(0,fn)
	fdDict[ fn ]["cache"].append( string )
```

這個做法除了實作LRU之外，還稍微做了些 hashing 和 memory cache 的優化。(說好的下次才講hashing勒！)

最原始的做法在第5行的地方其實可以直接用 `.format( Id )` 就好了，這樣每個檔案就是每個 Id 的內容了。然而如果今天是十幾萬個檔案，這樣做法其實很不效率，光讓他開開關關檔案就飽了。更學理一點的說法，當今天 cache 連基本的 working set 都存不下的時候，演算法花在 working set 搬移的時間就會讓演算法直接ＧＧ。因此我縮小 working set 的方法就是讓不同的 Id 都先存到同一個檔案，最後要整合的時候再把同一個檔案內的資料在 in memeory 的作法直接 group by 就好了。這樣可以有效的把 working set 減小又可以有效地使用記憶體。

另一個優化是做在**不要一直寫東西**。如果有接觸過資料庫理論的人，應該有接觸過 dirty buffer 這個概念，基本上就是資料寫入通常都不是真的寫入，是直到這個資源要被丟掉時才真的寫入硬碟。這裡概念也是一樣的。直到檔案要被關掉時，才把在我們快取的資料寫入，減少程式與作業系統間的溝通次數。

### Sloppy Conclusion

對我來說，思考演算法問題時常都是對症下藥。非常一般性的演算法對於很多變因都假設不知道或是假設最糟的狀況。然而，真正在應用時我們對於面對的問題是什麼樣的性質其實更加了解，可以更加精準的處理資料或是應用資源。

像是上面用 hashing 的方式在一般的狀況其實非常危險，因為如果有某個 Id 要寫入的資料極度肥大，那會讓 cache 的資料變得太多失去最一開始設計的目的。但是因為我確定基本上每個 Id 的資料量不會差距太大，這樣的手法不會讓我的記憶體爆炸。



*如果有什麼問題歡迎[寄信](mailto:eugene2528@gmail.com)給我，或是直接到 FB 上找我。我猜會看的都是認識我的人吧(笑)。*

*這些 code 其實之後我有空整理完也會出現在我的 github 裡面，目前還亂亂的躺在我自己 private repo 裡面(倒。*