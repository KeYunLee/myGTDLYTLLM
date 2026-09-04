---
title: "AI大神Karpathy的「LLM Wiki」是什麼？改善工作流、打造知識複利｜104職場力"
source: "https://blog.104.com.tw/andrej-karpathy-llm-wiki/"
author:
published:
created: 2026-09-05
description: "AI大神卡帕西（Andrej Karpathy）是特斯拉Autopilot神經網路架構推手、OpenAI共同創辦人，看看他如何運用「LLM Wiki模式」蓋出知識圖書館，這個模式的核心主張是，維護結構化知識庫的初期成本，會在成熟後帶來巨大的回報。"
tags:
  - "clippings"
---
## AI大神Karpathy的「LLM Wiki」是什麼？改善工作流、打造知識複利

2026.05.11

18067次觀看

![](https://blog.104.com.tw/wp-content/uploads/2026/05/11115150/Gemini_Generated_Image_678bbc678bbc678b-768x432.png)

AI大神卡帕西（Andrej Karpathy）是特斯拉Autopilot神經網路架構推手、OpenAI共同創辦人，看看他如何運用 **「LLM Wiki模式」** 蓋出知識圖書館，這個模式的核心主張是，維護結構化知識庫的初期成本，會在成熟後帶來巨大的回報。

**文／樂羽嘉編譯 **由 [天下雜誌](https://www.cw.com.tw/) 授權轉載****

**本文目錄（點擊可快速前往）**

每次問AI問題、得到答案，然後AI就失憶，下一次提問從零開始，是不是感覺很可惜？看看「AI大神」卡帕西怎麼幫AI蓋圖書館、累積知識複利。

每一位AI工程師都打造過類似的檢索流水線。你對文件做嵌入、切片、儲存向量，提問時回傳相關切片、由Claude或ChatGPT模型合成答案。

這套流程確實行得通，但如果3個月後有人問了一模一樣的的問題，系統只會機械式重複相同動作，沒有從前一次互動學習到任何東西。

這正是傳統RAG（檢索增強生成）的缺點，也就是 **從不保留知識** 。系統永遠處於健忘狀態，每一次查詢都必須從零開始，空有能力卻無法成長。

有AI大神之稱的卡帕西（Andrej Karpathy）是特斯拉Autopilot神經網路架構推手、OpenAI共同創辦人，透過課程教會一整個世代工程師深度學習。卡帕西發現這個問題，提出了一種架構上截然不同的處理方案── **「LLM Wiki模式」** 。

「LLM Wiki」是什麼？為什麼重要？

## LLM Wiki是什麼？卡帕西的真知灼見

卡帕西使用軟體工程學的「編譯」（Compilation）來做類比。

當你編寫原始碼時，編譯器會把原始碼轉換為優化的二進位產物。你不會每次都直接執行原始碼，你會編譯一次，分發產物，然後根據需求，有效率地跑原始碼。編譯過程雖然昂貴，但之後每次使用都能獲得回報。

傳統RAG直接針對原始碼去跑，每次查詢都要重新閱讀相同的論文、重新切分相同的PDF、重新綜合整理相同的答案，等於每次請求時都在執行原始碼。

**LLM Wiki模式則主張：先編譯你的知識。** 使用LLM閱讀你的來源資料，把內容綜合整理為結構化、互連的wiki頁面，然後針對這個「編譯產物」做所有查詢。綜合整理過程只發生一次，隨著補充更多來源增加、更新，預先組織好而且經過交叉引用的知識則能改善每次查詢品質。

這個編譯產物是一個wiki。不是資料庫，不是向量資料庫，是一組人類可讀、由 LLM維護的markdown檔案，一個概念一個檔案，有交叉引用、來源追蹤，透過Git做版本管理。

這是核心觀念的轉變，從「無狀態的檢索」轉向「有狀態、有複利效應的知識」。

## 三層知識架構

卡帕西的模式把知識組織為3個界限清晰的層級，每一層都明確的定位、嚴格的所有權模型。

### 第一層：原始來源（不可變層）

這是你的輸入層。包括研究論文、YouTube逐字稿、網路文章、會議記錄、部落格文章，任何你想讓系統學習的文本。把來源文件存放在sources/目錄下，當成不可變（Immutable）處理。LLM會閱讀它們，但絕不會修改，這些文本是推導出後續所有內容的基礎真理。

這種不可變性是刻意設計，有需要的時候你隨時可以從零開始重新推導出整個Wiki。

### 第二層：Wiki（由LLM維護）

這是模式的核心。Wiki是一系列\*.md檔案的集合，每個概念、主題或實體對應一個檔案，完全由LLM維護。人類閱讀Wiki，但不直接編寫，每次有新資料來源時，由LLM自動綜合整理、更新。

每個Wiki頁面都有YAML Front Matter（markdown檔案頂端存放後設資料的區域），用來追蹤標題、標籤、出處以及最後更新時間。頁面之間使用 \[\[slug\]\] 標記互相引用，使知識圖譜以純文本的形式清晰呈現。

### 第三層：schema（治理層）

schema是一個JSON檔案，定義整個頁面宇宙：Wiki追蹤哪些概念、它們的縮略名（Slugs）、標題、單行描述，等於是LLM執行人類意圖的合約。

當你希望系統開始追蹤一個新概念時，只要在schema中增加一個PageSpec（頁面規範）就好。下次吸收相關來源時，LLM就會自動建立、填寫這個頁面。若你想停止追蹤某個內容，就從schema拿掉。schema是人類唯一需要主動管理的部分，下游的一切都是自動化的。

也就是說，人類負責定義哪些知識應該存在，LLM則負責組織、整理這些知識並保持最新狀態。

## LLM Wiki核心主張：複利效應的豪賭

LLM Wiki模式的核心主張是，維護結構化知識庫的初期成本，會在成熟後帶來巨大的回報。

傳統RAG避開了這些成本，查詢時才做綜合整理，建構成本低、不需要設計 schema，但永遠不會變聰明。第1天和第1000天的同一個查詢，產生的答案品質完全一樣。

LLM Wiki逆轉這種取捨。匯入成本高、schema設計費神、維護需要定期校對，但維護了一年的wiki與全新的系統的品質差異極大，頁面佈滿交叉引用，答案融合了數十個來源，這種複利循環代表有價值的答案本身也成為知識庫的一部分。

## LLM Wiki實際上怎麼運作？

LLM Wiki架構很極簡，只有三個要素：

- **一個存放markdown檔案的資料夾：** 這就是你的知識庫，可以包含任何內容：研究筆記、會議摘要、專案文件、讀書筆記、個人參考資料、有附註的程式碼片段。
- **每個檔案內部要使用統一結構：** 好的LLM Wiki會使用一致的內部格式，包含標題、簡短摘要、主題標籤（Tags），接著才是內容。模型利用這種結構來更快速定位相關資訊。
- **用Claude Code當查詢介面：** 你打開終端機，切換到Wiki資料夾，啟動Claude Code，對它提問。Claude Code會讀取所需的檔案、合成答案，按照要求更新或新增筆記。

就這樣，不需要資料庫、不需要向量嵌入（你想要也可以加）、不需要伺服器。只有檔案和一個強大的模型。

Claude Code特別適合這項任務，因為它能直接從你的本地檔案系統讀取檔案。你不需要把內容複製貼上到聊天視窗，只要告訴它筆記在哪裡、直接提問就可以了。

## 怎麼用Obsidian建立你的LLM Wiki？

你可以使用Obsidian這個本地優先markdown編輯器，來建立工作流。你的檔案都保存在本機硬碟中，Obsidian只是用來讀寫這些檔案的工具。

### 第一步：安裝Obsidian、建立vault（資料庫）

從官方網站下載 Obsidian，建立新的vault，放在你容易記得的位置，例如~/wiki或~/Documents/llm-wiki。

在Obsidian中，「vault」只是一個資料夾，裡面全部都是純markdown檔案。

### 第二步：定義筆記模板

一致性是讓wiki可查詢的關鍵。建立一個模板檔案\_templates/note.md：

Markdown

\# undefined

\*\*Summary\*\*: 用一句話描述這則筆記  
\*\*Tags\*\*: #topic1 #topic2  
\*\*Created\*\*: 2026-04-06T00:00:00+00:00  
\*\*Last Updated\*\*: 2026-04-06T00:00:00+00:00

\---

\## Content

在這裡寫主要內容

\## Related Notes

\- \[\[Note Title\]\]

不需要完全照這個格式，重點是每篇筆記都要有一行摘要（Summary）、標籤（Tags），這樣Claude就算完全不讀整個檔案，也能快速判斷相關性。

### 第三步：用大分類資料夾整理

不要過度設計，先從4到5個頂層資料夾開始：

plaintext

Copy  
wiki/  
├── \_templates/  
├── projects/  
├── research/  
├── reference/  
├── meetings/  
└── inbox/

inbox/ 用來放還沒整理的筆記，之後讓Claude幫你分類。

### 第四步：開始寫第一批筆記

把對你最重要的知識先搬進來，例如常查的資料、整理好的流程、研究過的概念、做過的決策與這樣做決策的原因，不要一次全部匯入，最自然的方式是遇到新資訊就慢慢加入。

### 第五步：安裝Claude Code

先安裝Node.js，然後執行：

Bash

npm install -g @anthropic-ai/claude-code

登入你的Anthropic帳號後就可以使用。

### 第六步：查詢你的Wiki

打開終端機，進入你的wiki資料夾：

Bash

cd ~/wiki  
claude

然後可以問問題，像是：

**我有哪些關於機器學習的筆記？  
幫我整理research資料夾中關於RAG系統的內容  
我要寫關於X的提案，有哪些相關筆記？  
找出提到雨傘公司的筆記、摘要關鍵論點**

Claude會讀取你的markdown檔案，找出相關內容，給出有依據的回答，標註來源檔案，讓你可以驗證、深入挖掘。

## 建立LLM wiki的訣竅

- **寫摘要，不要只寫內容** ：每篇筆記開頭的一句摘要非常重要。Claude會先看這一行來判斷是否相關，花個10秒寫摘要，可以省掉模型讀錯文件的成本。
- **使用一致的術語** ：例如你有時寫「RAG」、有時寫「retrieval augmented generation」，模型可以理解，但是統一用詞會讓結果更乾淨。
- **建立筆記之間的連結：** Obsidian的 \[\[wiki link\]\] 會建立筆記之間的關係，Claude可以沿著這些連結推理。連結愈完整，你的知識系譜就愈強。
- **筆記要聚焦** ：比起餵一篇1萬字的大雜燴，不如拆成10篇1千字的主題筆記。內容愈聚焦，愈好查詢。
- **使用 /inbox 蒐集想法：** 不一定要追求完美，你可以把粗略、雜亂的筆記丟進 /inbox，再請Claude幫你整理，例如「幫我看inbox資料夾，建議每篇筆記該分類到哪裡，並加上標籤」。

## LLM Wiki有哪些優點？

**知識會隨著時間累積產生複利。** 這是核心優勢，每一份資料都會豐富同一個Wiki頁面，改善後續的所有查詢。一個匯入50篇關於Transformer論文的Wiki，在回答相關問題時，回答的深度和交叉引用的密度，會遠超過只對同樣 50篇論文進行切片的RAG系統。Wiki的答案整合全貌，RAG則只會翻出最相似的切片。

**Wiki變成熟後，查詢速度變快、變便宜。** 由於「綜合整理」發生在匯入階段而不是查詢階段，查詢過程就變成了針對預先組織好的材料進行「檢索＋生成」，那些吃力的LLM工作都已經事先做好了，包括理解來源、識別重點、撰寫清晰解釋、建立關聯。

**人類能閱讀知識庫。** 所有的產物都是純markdown檔案，工程師可以閱讀、編輯、對Wiki進行版本管理。如果LLM寫錯了，你可以直接修正。Git歷史紀錄提供了完整的稽核軌跡，沒有難以除錯的不透明嵌入資料庫或向量儲存。

**出處明確。** 每個wiki頁面都會記錄哪些來源文件有貢獻。如果一個頁面包含錯誤資訊，你可以追溯回原始來源。當某個來源更新或被證實不可信時，你清楚知道哪些頁面需要重新匯入資料。

**規模運作提高成本效率。** 在重複的綜合整理調用中，系統提示詞的快取機制能大幅降低API成本，路由機制能防止不必要的頁面重寫。成本高昂的綜合整理工作已經在匯入時預先完成，所以每次查詢的成本非常低。

**交叉引用的密度。** 隨著wiki持續擴充，頁面會累積用 \[\[slug\] 形式指向相關概念的交叉連結，這就是用純文字實現的知識圖譜。wiki發展成熟後，這種可導航的知識結構對查詢很有幫助。

## LLM Wiki有哪些缺點？

**大規模資料匯入緩慢、昂貴。** 對於每個來源，流程至少會對每個相關頁面呼叫兩次Claude（路由+綜合整理）。若匯入一個包含50篇論文、產出10個wiki頁面的語料庫，呼叫Claude API的次數約是100到200次。這對個人知識庫可行，但對企業級的大規模匯入流程來說成本較高。這個模式是適合最佳化精心挑選的可策展知識，比較不適合用來大量匯入文件。

**綜合整理的品質很依賴LLM。** wiki的品質取決於用來整理內容的模型，如果模型誤解來源、錯誤地合併兩個概念、憑空產生不存在的關聯（幻覺），這些錯誤會寫進wiki，持續影響後續所有查詢。所以這一步需要仔細設計提示詞、定期執行deep lint檢查。

**schema設計是一種負擔。** 決定要建立哪些頁面、如何命名、描述範圍怎麼界定都不簡單。設計不良的schema會導致：什麼都被判定為相關或什麼都抓不到，產生過於稀疏或過度膨脹的頁面。在新領域中，通常需要反覆迭代才能設計出好的schema。

**線性掃描限制規模。** 使用NumPy計算餘弦相似度的扁平化嵌入索引，在頁數達到約500頁以前都很有效。超過這個規模後，延遲會惡化，需要引入近似最近鄰（ANN）索引工具（如 FAISS、ChromaDB或Weaviate）。若要擴展至混合檢索（BM25+向量檢索），則需要額外成本。

**頁面過時風險。** 當你加入一個會大幅更新某個概念的新來源時，路由步驟應該要捕捉到、觸發「重新合成」，但如果頁面的schema描述太模糊或對齊不良，負責路由的LLM可能無法看出相關性，導致該頁面內容過時。雖然可以透過定期檢查程式碼或定期全面重新匯入來減少問題，還是需要主動維護。

**缺乏即時知識。** 這個模式不是為了即時串流數據（如X推文、股市數據、新聞API）設計的，對於需要即時更新的知識領域，wiki模式反而會增加摩擦而非提升效率。

（資料來源： [Medium](https://levelup.gitconnected.com/beyond-rag-how-andrej-karpathys-llm-wiki-pattern-builds-knowledge-that-actually-compounds-31a08528665e) 、 [MindStudio](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-knowledge-base-claude-code) ）

（原文標題： [AI大神教你改善工作流，用「LLM Wiki」打造知識複利](https://www.cw.com.tw/article/5140637) ）

**推薦閱讀：**

- [用AI做競品分析如何開始？7步驟看懂對手弱點，制定攻防策略](https://blog.104.com.tw/ai-competitive-analysis-strategy/)
- [最新模型Gemini 3.1 Pro　7個「深度工作」必備提示詞](https://pse.is/92awla)
- [專家：但願你能早點知道的8個ChatGPT好用方法](https://pse.is/92awme)
- [比提示工程更重要，「上下文工程」Context Engineering怎麼做？](https://pse.is/92awpn)

[AI](https://blog.104.com.tw/tag/ai/)

職場力滿意度調查

感謝您撥冗參與本次調查！請您在問卷中提供寶貴意見，以幫助我們改善和提升服務品質。

[![](https://blog.104.com.tw/wp-content/themes/oceanwp-child-theme-master/assets/img/icon-faq-mb.svg)](https://blog.104.com.tw/job_faq)

0

### 討論度排行

#### 1.

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/31141707/3881951_m-320x213.jpg)](https://blog.104.com.tw/2026-sports-coin-sweat-rewards-qa/)[

揮汗有禮懶人包》運動幣加碼活動資格、項目、登記、獎勵兌換通路QA

2026.08.31 ｜ 104小編

](https://blog.104.com.tw/2026-sports-coin-sweat-rewards-qa/)

#### 2.

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/26174805/pnizh11bjis-320x213.jpg)](https://blog.104.com.tw/genuine-respect-vs-superficial-compliance/)[

同事對你是「真尊重」還是「表面配合」？4個細節看懂你在職場的真實話語權！

2026.08.26 ｜ 104小編

](https://blog.104.com.tw/genuine-respect-vs-superficial-compliance/)

#### 3.

[![](https://blog.104.com.tw/wp-content/uploads/2022/11/17114553/app_og-768x375-1-320x156.png)](https://blog.104.com.tw/mobile-app-ios-and-android-20260817-update/)[

【工作快找App】求職條件自動補齊、個人資料編輯更直覺｜3.38.0 版本更新教學

2026.08.24 ｜ 104小編

](https://blog.104.com.tw/mobile-app-ios-and-android-20260817-update/)

#### 4.

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/28172500/ykahbrv8loy-320x213.jpg)](https://blog.104.com.tw/workplace-pioneers-power-withdrawal/)[

去大公司反而適應不良？「開創型人才」跳槽失敗，關鍵在戒不掉這3種隱形特權！

2026.08.28 ｜ 104小編

](https://blog.104.com.tw/workplace-pioneers-power-withdrawal/)

#### 5.

[![](https://blog.104.com.tw/wp-content/uploads/2026/09/01144832/33740893_m-320x213.jpg)](https://blog.104.com.tw/2026-motorcycle-zero-violation-lottery/)[

「機車常用族交通零違規抽獎活動」報名時間、資格、獎項及兌換│2026交通安全月

2026.09.01 ｜ 104小編

](https://blog.104.com.tw/2026-motorcycle-zero-violation-lottery/)

#### 6.

[![](https://blog.104.com.tw/wp-content/uploads/2026/09/03112017/%E8%81%AF%E7%99%BC%E7%A7%91%E5%9F%B7%E8%A1%8C%E9%95%B7%E8%94%A1%E5%8A%9B%E8%A1%8C%E3%80%82-%E8%A8%98%E8%80%85%E8%83%A1%E7%B6%93%E5%91%A8%EF%BC%8F%E6%94%9D%E5%BD%B1-320x213.jpg)](https://blog.104.com.tw/semicon-2026-mediatek-ceo-ai-capacity-win-win/)[

聯發科：創造AI時代三贏 CEO蔡力行喊話「拜託多給一點產能」

2天前 ｜ 104小編

](https://blog.104.com.tw/semicon-2026-mediatek-ceo-ai-capacity-win-win/)

### 求職攻略

[更多訂閱內容](https://blog.104.com.tw/category/job-search/)

[![](https://blog.104.com.tw/wp-content/uploads/2026/09/03140915/35201263_m-320x213.jpg)

面試官說「A公司比較適合你」怎麼答？急著反駁、猶豫都可能踩雷？

](https://blog.104.com.tw/inter-interviewer-says-another-company-suits-you/)

1天前 | 104小編

[![](https://blog.104.com.tw/wp-content/uploads/2026/09/02173006/%E8%81%AF%E5%90%88%E5%A0%B1%E7%B3%BB%E8%B3%87%E6%96%99%E7%85%A7-320x231.jpg)

統一超搶才！釋出5,000職缺 全家、萊爾富也端出快速加薪

](https://blog.104.com.tw/4-major-recruitment-benefits-for-convenience-stores/)

2026.09.02 | 104小編

#### [台積電效應22家優良供應商嘉義徵才 逾百職缺起薪4萬最高月薪6萬](https://blog.104.com.tw/tsmc-chiayi-joint-recruitment-2026/)

2026.08.26 | 104小編 | 1917觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/26163623/0rdboadnbwm-320x213.jpg)](https://blog.104.com.tw/tsmc-chiayi-joint-recruitment-2026/)

#### [職場老鳥面試更常犯這些錯！轉職換工作必看6大重點](https://blog.104.com.tw/talking-too-much-in-a-job-interview/)

2026.08.25 | 104小編 | 24031觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2021/08/05103806/pixta_74033438_M-320x213.jpg)](https://blog.104.com.tw/talking-too-much-in-a-job-interview/)

#### [領先中央！桃園解司機荒再創先例 祭33萬獎補助向僑外生招手](https://blog.104.com.tw/taoyuan-subsidizes-foreign-graduates-to-serve-as-bus-drivers/)

2026.08.24 | 104小編 | 1902觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/24172911/uon6www6khu-320x213.jpg)](https://blog.104.com.tw/taoyuan-subsidizes-foreign-graduates-to-serve-as-bus-drivers/)

### 最新影音上架

[更多影音內容 >](https://www.youtube.com/c/104Tw104Group)

### AI浪潮來襲

[更多訂閱內容](https://blog.104.com.tw/category/topic/ai/)

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/28174736/ChatGPT-Image-2026%E5%B9%B48%E6%9C%8828%E6%97%A5-%E4%B8%8B%E5%8D%8805_47_14-320x180.png)

專家：AI時代，3件事幫你保持職場競爭力

](https://blog.104.com.tw/ai-partner-not-crutch-career-competitiveness/)

2026.08.28 | 104小編

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/27153846/kqiykwghbtq-320x213.jpg)

零點擊時代來了：AISEO、GEO差在哪？如何才能「被AI引用」？

](https://blog.104.com.tw/aiseo-geo-ai-citation/)

2026.08.28 | 104小編

#### [AI進入3.0時代：智慧通膨、零點擊、人機協作如何改寫白領工作？](https://blog.104.com.tw/ai-3-white-collar-work/)

2026.08.27 | 104小編 | 1323觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/27151806/xm6dndrg2vw-320x213.jpg)](https://blog.104.com.tw/ai-3-white-collar-work/)

#### [ChatGPT怎麼整理Excel資料？清理欄位、統一格式，樞紐分析不出錯｜8組提示詞範本](https://blog.104.com.tw/chatgpt-excel-data-cleaning-prompt-guide/)

2026.08.24 | 104小編 | 1782觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/21135343/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2-2026-08-21-134207-1-320x180.png)](https://blog.104.com.tw/chatgpt-excel-data-cleaning-prompt-guide/)

#### [寫AI提示詞不該有的5個壞習慣！OpenAI最新指南建議「放手讓AI發揮」](https://blog.104.com.tw/5-bad-habits-of-ai-generated-suggestion-words/)

2026.08.22 | 104小編 | 2201觀看數

[![](https://blog.104.com.tw/wp-content/uploads/2026/08/21153357/carba5zxjtq-320x213.jpg)](https://blog.104.com.tw/5-bad-habits-of-ai-generated-suggestion-words/)