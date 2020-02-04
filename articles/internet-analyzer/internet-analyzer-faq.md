---
title: Internet Analyzer 常見問題 |Microsoft Docs
description: Azure Internet Analyzer 的常見問題。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184261"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure 網際網路分析器常見問題（預覽）

這是 Azure Internet Analyzer 的常見問題-如果您有其他問題，請前往意見反應[論壇](https://aka.ms/internetAnalyzerFeedbackForum)並張貼您的問題。 當問題經常被詢問時，我們會將它新增至本文，讓它可以快速且輕鬆地找到。

## <a name="how-do-i-participate-in-the-preview"></a>如何參與預覽？

此預覽可用來選取客戶。 如果您有興趣加入預覽，請執行下列動作：

1. 登入 [Azure 入口網站](https://ms.portal.azure.com)。
2. 流覽至 [**訂閱**] 頁面。
3. 按一下您打算搭配使用「網際網路分析器」的 Azure 訂用帳戶。
4. 移至訂用帳戶的**資源提供者**設定。
5. 搜尋 [ **Microsoft 網路**]，然後按一下 [**註冊**] \ （或 [**重新註冊**]）按鈕。
![存取要求](./media/ia-faq/request-preview-access.png)

6. 為我們提供您的電子郵件地址和用來提出存取要求的 Azure 訂用帳戶識別碼，以[要求核准](https://aka.ms/internetAnalyzerContact)。
7. 一旦您的要求獲得核准，您就會收到一封電子郵件確認，而且將能夠從新允許的 Azure 訂用帳戶建立/更新/修改網際網路分析器資源。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>我是否需要內嵌用戶端來執行測試？

是，您必須在應用程式中安裝網際網路分析器用戶端，以收集您的使用者特有的計量。 [瞭解如何安裝用戶端。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>參與預覽需要付費嗎？
否，Azure 網際網路分析器可在預覽中免費使用。 在預覽期間，沒有任何服務等級協定。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>什麼是 Internet Analyzer 設計用來處理的案例？

Internet Analyzer 的設計目的是要根據您的使用者群體提供網路效能深入解析。 為了協助您為使用者做出最佳的效能決策，Internet Analyzer 會使用您的不同使用者人數來比較兩個網際網路端點的效能。 雖然 Internet Analyzer 可以回答許多問題，但其中一些最常見的問題如下：

* 遷移至雲端有何效能影響？ 
    * *建議的測試：自訂（您目前的內部部署基礎結構）與 Azure （任何預先設定的端點）*
* 將資料放在邊緣與放在資料中心有何價值？ 
    *  *建議的測試： Azure 與 Azure Front 門板、Azure 與來自 Microsoft 的 Azure CDN*
* Azure Front 有何效能優勢？
    *  *建議的測試：自訂/Azure/CDN 與 Azure Front 門*
* 來自 Microsoft 的 Azure CDN 有何效能優勢？ 
    *  *建議的測試：自訂/Azure/AFD 與來自 Microsoft 的 Azure CDN*
* 來自 Microsoft 的 Azure CDN 如何堆疊？ 
    *  *建議的測試：自訂（其他 CDN 端點）與來自 Microsoft 的 Azure CDN*
* 在每個區域中何種雲端最適合您的終端使用者群體？ 
    *  *建議的測試：自訂（其他雲端服務）與 Azure （任何預先設定的端點）*

## <a name="which-tests-can-i-run-in-preview"></a>我可以在預覽中執行哪些測試？

您在 Internet Analyzer 中建立的每項測試都是由兩個端點組成：端點 A 和端點 B。下列任何組合都可以當做測試執行：  
* 兩個預先設定的端點，
* 一個自訂和一個預先設定的端點，或
* 兩個[自訂端點](internet-analyzer-custom-endpoint.md)（一個自訂端點必須位於 Azure 中）。

預覽期間提供下列預先設定的端點：
* **Azure 區域**
    * 巴西南部
    * 印度中部
    * 美國中部
    * 東亞
    * 美國東部
    * 日本西部
    * 北歐
    * 南非北部
    * 東南亞
    * 阿拉伯聯合大公國北部
    * 英國西部  
    * 歐洲西部
    * 美國西部
    * 美國西部 2
* **多個 Azure 區域組合**
    * 美國東部、巴西南部
    * 美國東部、東亞
    * 歐洲西部、巴西南部
    * 歐洲西部、東南亞
    * 歐洲西部、阿拉伯聯合大公國北部
    * 美國西部、美國東部
    * 美國西部、歐洲西部
    * 美國西部、阿拉伯聯合大公國北部
    * 歐洲西部、阿拉伯聯合大公國北部、東南亞
    * 美國西部、歐洲西部、東亞
    * 美國西部、北歐、東南亞、阿拉伯聯合大公國北部、南非北部 
* **Azure 與 Azure Front Door** - 部署於上列任意一個或多個 Azure 區域組合上
* **Azure 與來自 Microsoft 的 Azure CDN** - 部署於上列任意一個 Azure 區域組合上
* **Azure 與 Azure 流量管理員** - 部署於上列任意多個 Azure 區域組合上

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet Analyzer 與 Azure 所提供的其他監視服務有何不同？

Internet Analyzer 可協助您瞭解使用者的效能，並協助您制定決策來改善其效能。 雖然其他 Azure 監視工具可讓您深入瞭解您的 Azure 服務，但 Internet Analyzer 著重于測量使用者的端對端網際網路效能。

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>如何透過 Internet Analyzer 處理測量資料？

Azure 有[嚴密的安全性程序，並符合各種法規遵循標準](https://azure.microsoft.com/support/trust-center/)。 只有您和您指定的小組可以存取您的資料。 Microsoft 工作人員只有在您知情的特定有限情況下，才具有其限定存取權。 它會在傳輸中和待用時加密。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱我們的[網際網路分析器總覽](internet-analyzer-overview.md)。
