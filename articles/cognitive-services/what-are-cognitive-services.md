---
title: Azure 認知服務是什麼？
titleSuffix: Azure Cognitive Services
description: 認知服務讓每位開發人員都能存取 AI，而無需具備機器學習和資料科學專長。 您只需要從應用程式呼叫 API ，就能在應用程式中新增查看 (進階影像搜尋和辨識)、聆聽、說話、搜尋及決策的功能。
services: cognitive-services
author: nitinme
manager: nitinme
keywords: 認知服務, 認知智慧, 認知解決方案, ai 服務, 認知理解, 認知功能
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 82531466c80917087ca007900ca79b3485f38a21
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546257"
---
# <a name="what-are-azure-cognitive-services"></a>Azure 認知服務是什麼？

Azure 認知服務是使用 REST API 和用戶端程式庫 SDK 的雲端式服務，可協助您在應用程式中建立認知智慧。 您無需具備人工智慧 (AI) 或資料科學技能，就能將認知功能新增至應用程式。 Azure 認知服務包含各種可讓您建立認知解決方案的 AI 服務，以查看、聆聽、說出、了解，甚至做出決策。

## <a name="categories-of-cognitive-services"></a>認知服務的類別

認知服務的目錄可提供認知理解，分為五個主要支柱：

* 視覺
* 語音
* 語言
* 決策
* 搜尋

本文中的下列各節會提供屬於這五個支柱的服務清單。

## <a name="vision-apis"></a>視覺 API

|服務名稱|服務描述|
|:-----------|:------------------|
|[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "電腦視覺")|電腦視覺服務可供您存取進階演認知算法，以處理影像及傳回資訊。|
|[自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "自訂辨識服務")|自訂視覺服務可讓您建置自訂影像分類器。|
|[臉部](https://docs.microsoft.com/azure/cognitive-services/face/ "臉部")| 臉部辨識服務提供進階的臉部識別演算法，以偵測和辨識臉部屬性。|
|[表單辨識器](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "表單辨識器")|表單辨識器會從表單文件中識別並擷取索引鍵/值組和資料表資料；然後輸出結構化資料，包括原始檔案中的關聯性。|
|[筆跡辨識器](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "筆跡辨識器") (淘汰)|筆跡辨識器可讓您辨識並分析數位筆跡的筆觸資料、圖形和手寫內容，以及輸出包含所有已辨識實體的文件結構。|
|[影片索引子](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "影片索引子")|影片索引器可讓您從影片中擷取深入解析。|

## <a name="speech-apis"></a>語音識別 API

|服務名稱|服務描述|
|:-----------|:------------------|
|[語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "語音服務")|語音服務會將語音啟用功能加入應用程式。 語音服務包含各種功能，例如語音轉換文字、文字轉換語音、語音翻譯等等。|
<!--
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>語言 API

|服務名稱|服務描述|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Language Understanding 服務 (LUIS) 可讓應用程式了解人在文字中所表達的意思。|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker 可讓您從半結構化內容建置問題與解答服務。|
|[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "文字分析")| 文字分析可針對原始文字進行自然語言處理，進行情感分析、關鍵片語擷取和語言偵測。|
|[翻譯工具](https://docs.microsoft.com/azure/cognitive-services/translator/ "轉譯程式")|翻譯工具可提供近乎即時的機器文字翻譯。|
| [沈浸式閱讀程式](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/ "沈浸式閱讀程式") | 沈浸式閱讀程式在您的應用程式中新增了螢幕閱讀和理解功能。 |

## <a name="decision-apis"></a>決策 API

|服務名稱|服務描述|
|:-----------|:------------------|
|[異常偵測器](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "異常偵測器") |異常偵測器可讓您監視和偵測時間序列資料中的異常狀況。|
|[內容仲裁](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "內容仲裁")|內容仲裁可監視潛在的冒犯、惡意或具風險之內容。|
|[Metrics Advisor](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (預覽) | Metrics Advisor 可讓您在多變數的時間序列資料上提供可自訂的異常偵測，以及功能完整且可協助您使用服務的 Web 入口網站。|
|[個人化工具](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "個人化工具")|個人化工具可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。|

## <a name="search-apis"></a>搜尋 API

> [!NOTE]
> 尋找 [Azure 認知搜尋](https://docs.microsoft.com/azure/search/)？ 雖然其使用認知服務進行某些工作，但是可支援其他案例的不同搜尋技術。

|服務名稱|服務描述|
|:-----------|:------------------|
|[Bing 新聞搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing 新聞搜尋")|Bing 新聞搜尋會傳回一份與使用者查詢相關的新聞文章清單。|
|[Bing 影片搜尋](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing 影片搜尋")|Bing 新聞搜尋會傳回一份與使用者查詢相關的影片清單。|
|[Bing Web 搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing Web 搜尋")|Bing Web 搜尋會傳回一份與使用者查詢相關的搜尋結果清單。|
|[Bing 自動建議](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing 自動建議")|Bing 自動建議可讓您將部分搜尋查詢字詞傳送到 Bing，並取得建議查詢清單。|
|[Bing 自訂搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing 自訂搜尋")|Bing 自訂搜尋可讓您針對感興趣的主題，建立量身訂做的搜尋經驗。|
|[Bing 實體搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing 實體搜尋")|Bing 實體搜尋會傳回 Bing 判斷與使用者查詢相關的實體相關資訊。|
|[Bing 影像搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing 影像搜尋")|Bing 影像搜尋會傳回與使用者查詢相關的影像。|
|[Bing 圖像式搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing 圖像式搜尋")|Bing 圖像式搜尋會傳回深入解析的影像，例如視覺效果類似的影像、影像中找到的產品購物來源，以及相關搜尋。|
|[Bing 當地企業搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing 當地企業搜尋")| Bing 當地企業搜尋 API 可讓您的應用程式依搜尋查詢項目，尋找當地商家的連絡方式及位置資訊。|
|[Bing 拼字檢查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing 拼字檢查")|Bing 拼字檢查可讓您執行內容的文法與拼字檢查。|

## <a name="development-options"></a>開發選項 

有了 Azure 和認知服務，您可以存取數個開發選項，例如：

* 自動化和整合工具，例如 Logic Apps 和 Power Automate。
* 部署選項，例如 Azure Functions 和 App Service。 
* 可進行安全存取的認知服務 Docker 容器。
* 適用於巨量資料案例的 Apache Spark、Azure Databricks、Azure Synapse Analytics 和 Azure Kubernetes Service 等工具。 

若要深入了解，請參閱 [認知服務開發選項](./cognitive-services-development-options.md)。

## <a name="learn-with-the-quickstarts"></a>使用快速入門來了解

使用下列方式，透過實際操作快速入門開始建立認知服務資源：

* [Azure 入口網站](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure 入口網站")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK 用戶端程式庫](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager (ARM) 範本](resource-manager-template.md?tabs=portal "Azure Resource Manager (ARM) 範本")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>安全地使用認知服務

Azure 認知服務提供多層式安全性模型，包括透過 Azure Active Directory 認證、有效的資源金鑰及 [Azure 虛擬網路](cognitive-services-virtual-networks.md "Azure 虛擬網路")的[驗證](authentication.md "驗證 (authentication)")。

## <a name="containers-for-cognitive-services"></a>認知服務的容器

 認知服務在 Azure 雲端或內部部署中提供部署容器。 深入了解[認知服務容器](cognitive-services-container-support.md "認知服務容器")。

## <a name="regional-availability"></a>區域可用性

認知服務中的 API 裝載於持續成長且受 Microsoft 管理的資料中心網路上。 您可以在 [Azure 區域清單](https://azure.microsoft.com/regions "Azure 區域清單")中找到每個 API 的區域可用性。

尋找我們尚未支援的區域嗎？ 藉由在我們的 [UserVoice 論壇](https://cognitive.uservoice.com/ "UserVoice 論壇") \(英文\) 中提出功能要求，來讓我們知道。

## <a name="supported-cultural-languages"></a>支援的文化特性語言

認知服務以服務層支援各種不同的文化特性語言。 您可以在[支援的語言清單](language-support.md "支援的語言清單")中找到每個 API 的語言可用性。

## <a name="certifications-and-compliance"></a>認證和合規性

認知服務已獲得認證，例如 CSA STAR 認證、FedRAMP Moderate 和 HIPAA BAA。 您可以[下載](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "下載這篇文章")適用於自身稽核及安全性審查的認證。

若要了解隱私權和資料管理，請前往[信任中心](https://servicetrust.microsoft.com/ "信任中心")。

## <a name="support"></a>支援

認知服務提供數個支援選項，協助您持續建立智慧型應用程式。 認知服務也有一群強大的開發人員，可協助回答您的特定問題。 如需可用選項的完整清單，請參閱 [認知服務支援和協助選項](cognitive-services-support-options.md "認知服務支援和協助選項")。

## <a name="next-steps"></a>後續步驟

* [建立認知服務帳戶](cognitive-services-apis-create-account.md "建立認知服務帳戶")
* [認知服務文件中有何新資訊](whats-new-docs.md "認知服務文件中有何新資訊")
