---
layout: post
title: Retrieval and Richness When Querying By Document
tags:
image: +search.png
comments: true
---

這篇文章發表在 DESIRES 2018。這個系列的部落格規劃將會重新寫一段稍長一點中文摘要融合多一點的背景知識並附上原本文章的英文摘要。然而這個中文摘要並不會太正式，所以有些專有名詞就不翻譯了(其實是懶呵呵)。最後會附上文章的連結還有投影片。歡迎有興趣的人一起來多加討論。

文章全文連結: [Retrieval and Richness When Querying By Document](http://desires.dei.unipd.it/papers/paper12.pdf)

投影片連結: [Google Drive](https://drive.google.com/file/d/1_ex_k7Y8RiEvBmSRZfkHpMEz0qKZmVxi/view?usp=sharing)

## 中文摘要

在 Information Retrieval 中，我們一般都會有一個使用者所下的關鍵字。即使我們需要做 Query Reformulation，這組關鍵字仍然會是非常關鍵的資訊。然而如果我們只有一個使用者所提供的範例呢？在 ad hoc retrieval 中，我們可以將單一一個例子，或是 relevant document，視為一個又臭又長的關鍵字；而在機器學習中，我們也可以將這個例子視為唯一的 training data。我們稱這樣的問題為 Query by Document (QBD) 。我們使用 RCV1-v2 資料集中的 658 個類別並使用 Elasticsearch 作為我們研究的工具。基於這個研究與法律上的搜尋有很高的關聯，我們也將一起討論類別 Richness 與 QBD 之間的關係。我們發現，經典的 BM25 搜尋模型提供了最好的表現。而過去研究幾乎一致認為必要的 Query Reduction(減少關鍵字) ，我們也發現並不必然帶給我們更好的搜尋結果。更有趣的是，雖然類別的 Richness 在某種意義上代表著這個類別的難易程度，Richness 和搜尋結果呈現的正相關仍然超乎我們預期的明顯。除此之外，在我們試圖在機器學習 Scikit-learn 套件中重製 Elasticsearch 結果的同時，我們也發現了些開源軟體 Lucene 中一些值得探討的問題。

## 英文摘要

A single relevant document can be viewed as a long query for ad hoc retrieval, or a tiny training set for supervised learning. We tested techniques for QBD (query by document), with an eye toward their eventual use in active learning of text classifiers in a legal context. Richness (prevalence of relevant documents) varies widely in our tasks of interest. We used 658 categories from the RCV1-v2 collection to study the impact of richness on QBD variants supported by Elasticsearch. BM25 weighting on full query documents dominated other methods. However, its absolute and relative effectiveness depended strongly on richness, raising broader questions about common test collection practices. We ported Elasticsearch’s version of BM25 to the machine learning package scikit-learn and we discuss some lessons learned about the replicability of retrieval results.



*曾經想說每篇上的 paper 都要來寫一篇部落格介紹一下，結果 DESIRES 都結束多久了還是沒有寫出來。總算在飛機上有一點點空檔把這篇文章打出來。當初有這樣部落格的構想一方面是基於想要整理並分享自己的研究結果，另一方面也是認為語言不應該成為學習或瞭解知識的障礙。雖然這樣的障礙是這個時代必然存在的事實，但是我仍然覺得在能力範圍內，知識的推廣也是作為一個研究者應該要做的事情。*

*如果大家有哪想要了解 IR 哪個部分，如果我剛好也有涉獵可以建議(逼)我寫篇部落格出來 :)*