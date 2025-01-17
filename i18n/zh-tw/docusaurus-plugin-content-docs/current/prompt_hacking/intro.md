---
sidebar_position: 0
---



# 🟢 介詔

import Lock from '@site/static/img/lock.webp';

<div style={{textAlign: 'center'}}>
  <img src={Lock} style={{width:"30%"}}/>

</div>


提示駭客攻擊(Prompt hacking)是一個術語，用於描述透過操縱 %%LLMs|LLM%% 的輸入或提示來利用其漏洞的攻擊類型。與通常利用軟體漏洞的傳統駭客攻擊不同，提示駭客攻擊依賴精心設計的提示來欺騙 LLM 執行意想不到的操作。

我們將介紹三種類型的提示駭客攻擊：提示注入(prompt injection)、提示洩漏(prompt leaking)和越獄(jailbreaking)。提示注入涉及將惡意或非預期內容添加到提示中以劫持語言模型的輸出。

提示洩露和越獄實際上是其中的子集：提示洩露涉及從 LLM 的回應中提取敏感或機密信息，而越獄則涉及繞過安全和審核功能。我們還將討論具體的進攻技術和防守技術。

為了防止提示駭客入侵，必須採取防禦措施。其中包括實施基於提示的防禦、定期監控 LLM 的行為和輸出是否有異常活動，以及使用微調或其他技術。總體而言，提示駭客攻擊是 LLM 安全性日益受到關注的問題，因此必須保持警惕並採取主動措施來防範此類攻擊。