---
layout: post
title: Keyword Extraction
tags: []
image: +search.png
comments: true
---

好，忙到一個小段落可以來寫個文章。Keyword Extraction 是個很大的主題，我也只能就我知道的方向討論，歡迎大家在下面留言跟我聊聊哈哈。

首先，所謂 Keyword Extraction 顧名思義我們想要從文章中找尋關鍵字。然而，關鍵字的概念其實是相對的。如果今天有兩篇文章，一篇是資工的學術文章，一篇是蘋果日報的八卦廢文。如果在完全沒有背景知識下，你大概只能就兩篇文章各自的重點來尋找關鍵字，然而**你說說到底啥是重點啊？？？？**

對於沒有背景知識的人來說，所謂的重點和不是重點其實很難區分。通常最簡單的做法可能是，讓我們來看看類似的文章或是同個領域的文章，其他文章沒有但是這篇文章有，那可能就是重點了。你可能會覺得，這是什麼搞笑的做法，誰會這麼讀文章？就算是蘋果的八卦文章，重點可能是~~哪個小模，去了哪邊拍寫真集，或是在哪裡被拍到跟誰摟腰等等~~。你可能會試著了解文章的內容，建立文章的觀念系統，然後尋找真正重要的概念。

所以讓我們好好定義今天要討論的問題：**對於一篇給定的文件，我們希望尋找到可以在語義上代表這個文件的關鍵字或關鍵詞。** *(For a given written document, we try to find the words or phrases that best represent the semantic meaning of the original document.)*

好了，直覺的方法很多，我廢話也講了不少，所以到底要怎麼找關鍵字？別急，Keyword Extraction 說到底仍然是個自然語言分析 (NLP) 的問題，我們先來看看在語言學上分析語言的層次，再來看看個別方法的長處。

# 從 NLP 來看 Keyword Extraction

![Machine Translation Plan](https://upload.wikimedia.org/wikipedia/commons/thumb/7/79/Major_levels_of_linguistic_structure.svg/500px-Major_levels_of_linguistic_structure.svg.png)

*圖片取自 [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Major_levels_of_linguistic_structure.svg)*

這張由內而外表達了在語言學上對於語意分析的層次。畢竟我真的不是 NLP 的專家，有錯還請告訴我Orz。最裡面的聲韻學 Phonology & Phonetics 我們就不提了，畢竟文章的文字不會自己說話嘿嘿。

再來的 Morphology 討論的是英文每個字可能會有的標記，像是過去式 +ed，最高級 +est 等等的，讓我們對於每個字所表達的意思可以有所了解。 Syntax 討論的就會是字與字的組成，也就是文法，例如：一個句子 Sentance 會有名詞子句 Noun Phrase(NP) 及動詞子句 Verb Phrase(VP) 組成等等。**到這裡，我們都還沒有提到任何關於語意的問題。**

再來， Semantics 所研究的事就是由各個單字 words 或片語 phrases 所組成的語意。就目前 NLP 的研究發展，我們開始慢慢有系統的整理記錄還有預測文字所代表的意思。你可能會覺得 Siri 都知道我在嗆她了，這有什麼難的？**這超難的！**目前的系統很多都是 rule-based 靠超級多規則來判斷意思，沒辦法單純從文字的組成及單字的意思來了解。如果有興趣可以搜尋 Semantic Representation 或是我比較有接觸的 AMR 可以了解更多。

最後的 Pragmatics 是目前 NLP 完全還沒有能力碰到的領域。這研究的是語境下語言意義的改變，這在 NLP 上目前還沒有有系統的研究方法，或是可規模化的研究。當然拿 Twitter 資料預測反諷或是阿拉伯文在不同的場合所使用的文字及代表的意思都是目前很活躍的研究領域，這些都跟 Pragmatics 有關。

好了，再不說 Keyword Extraction 我就要被打了。

# Keyword Extraction 的方法

好，來到方法。我自己認為可以將這些方法從概念上放到光譜上，左右分別是：Syntax-based 及 Semantic-based。我們可以在不了解文字意義下試圖尋找關鍵字，也可以從語意上出發，當然也有方法是介於兩者之間的。

這些方法的前提都是：**你有很多資料可以拿來當作 training data**。

## Co-occurance Method

這類型的方法主要用到 TF-iDF 的觀念。由於我超級懶，不想要再認真寫他的內容，所以勒直接引用[好友大維的文章](https://taweihuang.hpd.io/2017/03/01/tfidf/)。值得多加一提的是，我們在建立這些 term 或是更廣義的說是 token 時，不只可以用 Bag of Word 也可以用 Bag of N-gram 或是用特殊的 Name Entity Tagger。

所謂的 word n-gram 就是無腦的移動框框來建立可能的片語。舉例而言： New York is one of the biggest city in the United States。所謂的 2-gram 就會是 New York, York is, is one, one of, of the, the biggest, biggest city, city in, in  the, the United, United State 這麼多項。我們想要的片語包含在這些可能的片語當中，例如 New York, United States。我們可以透過 TF-iDF 來找出分數高的 2-gram，進而刪掉那些沒用的。

詳細的方法可以參照[這篇 Brain Lott 的 Servey Paper](http://www.cs.unm.edu/~pdevineni/papers/Lott.pdf) 。

單純的使用 TF-iDF ，我們可以選取分數高的 token 當作我們要找的 keywords 。稍微複雜一點的方法，我們可以進一步利用 Topic Modeling 來幫忙。

## Topic Modeling

我們可以利用

## Lexical Chains

## Semantic Representation Graph

cite https://cs.stanford.edu/people/jure/pubs/nlpspo-msrtr05.pdf

