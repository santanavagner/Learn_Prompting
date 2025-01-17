---
sidebar_position: 40
---

# 🟢 聊天機器人 + 知識庫

import ImageIntents from '@site/docs/assets/basic_applications/chatbot_from_kb_intents.webp'
import ImageGPT3 from '@site/docs/assets/basic_applications/chatbot_from_kb_gpt3.webp'
import ImageGPT3Organized from '@site/docs/assets/basic_applications/chatbot_from_kb_gpt3_organized.webp'
import ImagePrompt from '@site/docs/assets/basic_applications/chatbot_from_kb_prompt.webp'
import ImageLogin from '@site/docs/assets/basic_applications/chatbot_from_kb_login.webp'

從 [GPT-3](https://arxiv.org/abs/2005.14165) 和 [ChatGPT](https://chat.openai.com/chat) 等大型語言模型 (LLM) 的最新進展中可以看出，在技術行業引起了很大的關注。這些模型對內容生成非常強大，但它們也有一些缺點，例如偏差(@nadeem-etal-2021-stereoset) 和幻覺(@Ji_2022)。LLM 在聊天機器人開發方面特別有用。
”
## 基於意圖的聊天機器人

傳統聊天機器人通常是基於意圖的(intent-based)，這意味著它們被設計為響應特定的使用者意圖。每個意圖由一組樣本問題和相應的響應組成。例如，"天氣" 意圖可能包括類似 "今天天氣如何？" 或 "今天會下雨嗎？" 這樣的樣本問題，並且可能輸出 "今天將是晴天" 的響應。當用戶提出問題時，聊天機器人將其與最相似的樣本問題匹配意圖，並返回相應的響應。

<div style={{textAlign: 'left'}}>
  <img src={ImageIntents} style={{width: "700px"}}/>
  <p style={{color: "gray", fontSize: "12px", fontStyle: "italic"}}>傳統基於意圖的聊天機器人的工作原理。影象由作者製作。</p>
</div>

然而，基於意圖的聊天機器人也有自己的問題。其中一個問題是，它們需要大量特定的意圖才能給出特定的答案。例如，使用者說 "我無法登入"、"我忘記了密碼" 或 "登入錯誤" 等話語可能需要三個不同的答案和三個不同的意圖，儘管它們都非常相似。

## GPT-3 如何幫助

這就是 GPT-3 可以發揮的特別用處。每個意圖可以更廣泛，利用您的知識庫文件。知識庫 [Knowledge Base](https://en.wikipedia.org/wiki/Knowledge_base) 是儲存為結構化和非結構化資料的資訊，可用於分析或推斷。您的知識庫可能由一系列文件組成，解釋如何使用您的產品。

因此，每個意圖與文件相關聯，而不是一組問題和特定答案，例如，一個 "登入問題" 的意圖，一個 "如何訂閱" 的意圖等等。當用戶詢問有關登入的問題時，我們可以將 "登入問題" 文件傳遞給 GPT-3 作為上下文資訊，併為使用者的問題生成特定的響應。

<div style={{textAlign: 'left'}}>
  <LazyLoadImage src={ImageGPT3} style={{width: "700px"}} />
  <p style={{color: "gray", fontSize: "12px", fontStyle: "italic"}}>利用GPT-3的聊天機器人工作原理。作者提供的圖片。</p>
</div>

這種方法減少了需要處理的意圖數量，並允許更好地適應每個問題的答案。此外，如果與意圖關聯的文件描述了不同的流程（例如 "在網站上登入" 的流程和 "在移動應用程式上登入" 的流程），GPT-3 可以在給出最終答案之前自動詢問使用者以獲得更多的上下文資訊。

## 為什麼不能將整個知識庫傳遞給 GPT-3？

今天，像 GPT-3 這樣的 LLM 模型的最大提示的長度約為 4k 令牌（對於[`text-davinci-003`](https://beta.openai.com/docs/models/gpt-3)模型），這很多，但不足以將整個知識庫塞入單個提示中。 LLM 由於計算原因具有最大提示的長度限制，因為使用它們生成文字涉及多個計算，隨著提示大小的增加，計算量也會迅速增加。

未來的 LLM 可能不會有這種限制，同時保留文字生成能力。然而，就目前而言，我們需要一個設計解決方案來解決這個問題。

## 如何使用 GPT-3 構建一個聊天機器人

聊天機器人的流程可以分為以下兩個步驟：

首先，我們需要為使用者的問題選擇適當的意圖，即我們需要從知識庫中檢索正確的文件。
然後，一旦我們有了正確的文件，我們就可以利用 GPT-3 為使用者生成適當的答案。在這樣做的過程中，我們需要精心製作一個良好的提示。

第一步可以使用語義搜尋[semantic search](https://en.wikipedia.org/wiki/Semantic_search)解決。我們可以使用[`sentence-transformers`](https://www.sbert.net/examples/applications/semantic-search/README.html)庫中的預訓練模型，輕鬆地為每個文件分配一個分數。分數最高的文件將用於生成聊天機器人答案。

<div style={{textAlign: 'left'}}>
  <LazyLoadImage src={ImageGPT3Organized} style={{width: "700px"}} />
  <p style={{color: "gray", fontSize: "12px", fontStyle: "italic"}}>如何利用 GPT-3 讓聊天機器人工作。GPT-3 可以利用知識庫文件中的資訊生成適當的答案。圖片由作者提供。</p>
</div>

## 使用 GPT-3 生成答案


一旦我們有了正確的文件，我們需要建立一個好的提示，以便將其用於 GPT-3 生成答案。在以下實驗中，我們將始終使用 `temperature` 為 0.7 的 `text-davinci-003` 模型。

為了製作提示，我們將嘗試使用以下內容

- [**角色提示**](https://learnprompting.org/docs/basics/roles): 一種啟發式技術，為 AI 設定特定的角色。
- **相關的知識庫資訊**: 即在語義搜尋步驟中檢索到的文件。
- **使用者和聊天機器人之間最後一次交換的訊息**: 這對於使用者傳送的未指定整個上下文的訊息非常有用。我們將在後面的例子中看到它。請檢視[此範例](https://learnprompting.org/docs/applied_prompting/build_chatgpt) 瞭解如何使用　GPT-3　管理對話。
- **使用者的問題**。

<div style={{textAlign: 'left'}}>
  <LazyLoadImage src={ImagePrompt} style={{width: "700px"}} />
  <p style={{color: "gray", fontSize: "12px", fontStyle: "italic"}}>Information used to craft our GPT-3 prompt. Image by the author.</p>
</div>

讓我們使用<span className="yellow-highlight">角色提示</span>技術開始我們的提示。

<pre>
    <span className="yellow-highlight">作為一名高階聊天機器人 Skippy，您的主要目標是盡力幫助使用者。</span><br/>
</pre>

然後，假設語義搜尋步驟從我們的知識庫中提取了以下文件。所有文件都描述了類似於　Instagram　的虛構產品　VideoGram　的工作方式，但僅用於影片。

<div style={{textAlign: 'left'}}>
  <LazyLoadImage src={ImageLogin} style={{width: "700px"}} />
  <p style={{color: "gray", fontSize: "12px", fontStyle: "italic"}}>解釋 VideoGram 登入方式的文件。作者提供的影像。</p>
</div>

我們可以在提示中這樣新增<span className="yellow-highlight">它的內容</span>。

<pre>
作為一款名為 Skippy 的高階聊天機器人，您的主要目標是盡力幫助使用者。<br/><br/>

    <span className="yellow-highlight">
    開始上下文<br/>
    從網站登入到 VideoGram<br/>
    1. 開啟您的網路瀏覽器並前往 VideoGram 網站。<br/>
    2. 單擊頁面右上角的"登入"按鈕。<br/>
    3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
    4. 輸入憑據後，單擊"登入"按鈕。<br/>
    5. 您現在應該已經登入到您的 VideoGram 帳戶中。<br/>
    <br/>
    從移動應用登入到 VideoGram<br/>
    1. 在您的移動裝置上開啟 VideoGram 應用。<br/>
    2. 在主頁面上，點選位於右下角的"登入"按鈕。3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
    4. 輸入憑據後，點選"登入"按鈕。<br/>
    5. 您現在應該已經登入到您的 VideoGram 帳戶中。<br/>
    結束上下文<br/>
    </span>
</pre>

最後，我們需要將<span className="yellow-highlight">對話和使用者問題</span>新增到提示的末尾，就像以下範例中那樣。

<pre>
作為一款名為 Skippy 的高階聊天機器人，您的主要目標是盡力幫助使用者。<br/>
<br/>
  開始上下文<br/>
  從網站登入到 VideoGram<br/>
  1. 開啟您的網路瀏覽器並前往 VideoGram 網站。<br/>
  2. 單擊頁面右上角的"登入"按鈕。<br/>
  3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
  4. 輸入憑據後，單擊"登入"按鈕。<br/>
  5. 您現在應該已經登入到您的 VideoGram 帳戶中。<br/>
  <br/>
  從移動應用登入到 VideoGram<br/>
  1. 在您的移動裝置上開啟 VideoGram 應用。<br/>
  2. 在主頁面上，點選位於右下角的"登入"按鈕。3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
  4. 輸入憑據後，點選"登入"按鈕。<br/>
  5. 您現在應該已經登入到您的 VideoGram 帳戶中。<br/>
  結束上下文<br/>
  <br/>
  <span className="yellow-highlight">SKIPPY:您好，我是 Skippy！我能為您做些什麼？<br/>
  使用者:我找不到登入按鈕。<br/>
  SKIPPY:</span><br/>
</pre>

將此提示傳遞給 GPT-3，我得到了以下生成的答案。

<pre>
<span className="bluegreen-highlight">沒問題！ 在網站上，登入按鈕位於頁面右上角。 在移動應用程式上，登入按鈕位於頁面右下角。如果您需要進一步的幫助，請告訴我！</span>
</pre>
請注意，生成的答案中的資訊來自知識庫文件。

## 用 GPT-3 消歧使用者問題

我們來試試更模糊的使用者輸入，比如<span className="yellow-highlight">"無法登入"</span>。這是完整的提示。

<pre>
    作為一個名叫 Skippy 的高階聊天機器人，您的主要目標是盡力幫助使用者。<br/>
    <br/>
    START CONTEXT<br/>
    從網站登入到 VideoGram<br/>
    1. 開啟您的 Web 瀏覽器，然後轉到 VideoGram 網站。<br/>
    2. 點選頁面右上角的"登入"按鈕。<br/>
    3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
    4. 輸入您的憑據後，單擊"登入"按鈕。<br/>
    5. 您現在應該已經登入到 VideoGram 帳戶。<br/>
    <br/>
    從移動應用程式登入到 VideoGram<br/>
    1. 開啟您的移動裝置上的 VideoGram 應用程式。<br/>
    2. 在主頁面上，點選右下角的"登入"按鈕。 3. 在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。<br/>
    4. 輸入您的憑據後，點選"登入"按鈕。<br/>
    5. 您現在應該已經登入到 VideoGram 帳戶。<br/>
    END CONTEXT<br/>
    <br/>
    SKIPPY: 你好，我是 Skippy！我能幫你什麼忙？<br/>
    USER: <span className="yellow-highlight">無法登入</span><br/>
    SKIPPY:<br/>
</pre>

GPT-3 試圖更好地理解使用者的問題，給出了以下回答。

<pre>
    <span className="bluegreen-highlight">很抱歉聽到這個訊息。您能告訴我您是在嘗試透過 VideoGram 網站還是移動應用程式登入嗎？</span>
</pre>

接下來，這是完整的對話內容。為了方便檢視，這裡省略了提示的其他部分。

<pre>
    USER: 無法登入<br/>
    SKIPPY: 很抱歉聽到這個訊息。您能告訴我您是在嘗試透過 VideoGram 網站還是移動應用程式登入嗎？<br/>
    USER: 移動應用程式<br/>
    <span className="bluegreen-highlight">SKIPPY: 好的，沒問題。要透過移動應用程式登入，請開啟您的移動裝置上的 VideoGram 應用程式。在主頁面上，點選右下角的"登入"按鈕。在登入頁面上，輸入您的 VideoGram 使用者名稱和密碼。輸入您的憑據後，點選"登入"按鈕。您現在應該已經登入到 VideoGram 帳戶。如果您需要進一步的幫助，請告訴我。</span><br/>
</pre>

只要有正確的上下文資訊，GPT-3 就可以進行消除歧義。

## 使用 GPT-3 生成答案時可能出現的問題

前面的例子都很正常執行。然而，這種聊天機器人可能會失敗的幾種方式。

如果我們詢問 "移動應用程式是否免費？"，將登入文件作為上下文傳遞給 GPT-3，你通常會得到一個答案，比如 "是的，VideoGram 移動應用程式是免費下載和使用的"，即使這樣的資訊並沒有包含在上下文資訊中。生成虛假資訊對於客戶服務聊天機器人來說非常糟糕！

當用戶問題可以在上下文中找到答案時，GPT-3 很少生成虛假資訊。由於使用者問題通常是短小模糊的文字，我們不能總是依賴語義搜尋步驟來檢索正確的文件，因此我們總是容易受到虛假資訊生成的影響。

## 結論

GPT-3 非常適用於建立對話式聊天機器人，並能夠根據插入的上下文資訊回答一系列具體問題。然而，僅依靠上下文資訊使模型產生答案很困難，因為模型往往會產生幻覺（即生成新資訊，可能是錯誤的）。生成虛假資訊是一個不同嚴重程度的問題，這取決於使用情況。

Written by [Fabio Chiusano](https://www.linkedin.com/in/fabio-chiusano-b6a3b311b/).
