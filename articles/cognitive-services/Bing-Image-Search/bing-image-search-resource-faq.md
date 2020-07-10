---
title: 常見問題集 (FAQ) - Bing 影像搜尋 API
titleSuffix: Azure Cognitive Services
description: 提供 Bing 影像搜尋 API 相關概念、程式碼、案例等常見問題的解答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 2d919640431ce014d34372812d9c07bb84d750b2
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184050"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Bing 影像搜尋 API 的常見問題集 (FAQ)

尋找 Azure 上 Microsoft 認知服務的 Bing 影像搜尋 API 相關概念、程式碼和案例常見問題的解答。

## <a name="response-headers-in-javascript"></a>JavaScript 中的回應標頭

下列標頭可能會發生於來自 Bing 影像搜尋 API 的回應。

| 屬性           | 描述   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Bing 已指派給使用者的唯一識別碼 |
| `BingAPIs-Market`   |用來履行要求的市場 |
| `BingAPIs-TraceId`  |這項要求 (適用於支援) 的 Bing API 伺服器上的記錄項目 |

保存用戶端識別碼，並將它與後續要求一起傳回尤其重要。 當您這麼做時，搜尋會在排名搜尋結果中使用過去的內容，也會提供一致的使用者體驗。

不過，當您從 JavaScript 呼叫 Bing 影像搜尋 API 時，您的瀏覽器內建安全性功能 (CORS) 可能會讓您無法存取這些標頭的值。

若要取得標頭的存取權，您可以透過 CORS Proxy 提出 Bing 影像搜尋 API 要求。 來自這類 Proxy 的回應包含 `Access-Control-Expose-Headers` 標頭，可將回應標頭列入允許清單並提供給 JavaScript 使用。

您可以輕鬆地安裝 CORS Proxy，讓我們的[教學課程應用程式](tutorial-bing-image-search-single-page-app.md)存取選擇性用戶端標頭。 首先，請[安裝 Node.js](https://nodejs.org/en/download/) (若尚未安裝)。 在命令提示字元中，輸入下列命令。

```console
npm install -g cors-proxy-server
```

接下來，將 HTML 檔案中的 Bing 影像搜尋 API 端點變更為： \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

最後，使用下列命令啟動 CORS Proxy：

```console
cors-proxy-server
```

當您使用教學課程應用程式時，請保持開啟命令視窗；關閉視窗會停止 Proxy。 在可展開的 [HTTP 標頭] 區段搜尋結果下，您現在可以看到 `X-MSEdge-ClientID` 標頭 (及其他標頭)，並確認每個要求的此標頭都相同。

## <a name="response-headers-in-production"></a>生產環境中的回應標頭

前一個解答中描述的 CORS Proxy 方法也適用於開發、測試和學習。

不過，在生產環境中，您應該在與使用 Bing Web 搜尋 API 的網頁相同的網域上，裝載伺服器端指令碼。 此指令碼應該在收到來自網頁 JavaScript 的要求時實際進行 API 呼叫，並將所有的結果 (包括標頭) 傳回給用戶端。 因為兩個資源 (頁面和指令碼) 會共用來源，所以 CORS 不會開始生效，且特殊標頭可供網頁上的 JavaScript 存取。

這個方法也會保護您的 API 金鑰，以免對大眾公開，因為只有伺服器端指令碼需要它。 此指令碼可以使用其他方法 (例如 HTTP 參照位址) 來確定要求已獲得授權。

## <a name="next-steps"></a>後續步驟

您的問題是否與缺少特性或功能相關？ 請考慮在我們的 [User Voice 網站](https://cognitive.uservoice.com/forums/555907-bing-search)上要求或對它投票。

## <a name="see-also"></a>另請參閱

 [Stack Overflow：認知服務](https://stackoverflow.com/questions/tagged/bing-api)
