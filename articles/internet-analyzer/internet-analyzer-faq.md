---
title: 互聯網分析器常見問題 |微軟文檔
description: Azure Internet 分析器的常見問題解答。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184261"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure 互聯網分析器常見問題解答（預覽版）

這是 Azure Internet 分析器的常見問題解答 - 如果您有其他問題，請轉到[回饋論壇](https://aka.ms/internetAnalyzerFeedbackForum)併發布您的問題。 當問題經常被問到時，我們會將其添加到本文中，以便快速輕鬆地找到它。

## <a name="how-do-i-participate-in-the-preview"></a>如何參與預覽？

預覽版可用於選擇客戶。 如果您有興趣加入預覽版，請執行以下操作：

1. 登錄到 Azure[門戶](https://ms.portal.azure.com)。
2. 導航到 **"訂閱"** 頁。
3. 按一下計畫使用 Internet 分析器的 Azure 訂閱。
4. 轉到**訂閱的資來源提供者**設置。
5. 搜索**Microsoft.Network**並按一下 **"註冊**（或**重新註冊**）"按鈕。
![訪問請求](./media/ia-faq/request-preview-access.png)

6. 通過向我們提供您的電子郵件地址和用於發出訪問請求的 Azure 訂閱 ID[來請求批准](https://aka.ms/internetAnalyzerContact)。
7. 請求獲得批准後，您將收到一封電子郵件確認，並能夠從新允許的 Azure 訂閱創建/更新/修改 Internet 分析器資源。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>我需要嵌入用戶端來運行測試嗎？

是的，必須在應用程式中安裝 Internet 分析器用戶端來收集特定于使用者的指標。 [瞭解如何安裝用戶端。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>我參加預覽會需要收費嗎？
否，Azure Internet 分析器可免費在預覽版中使用。 預覽期間沒有服務等級協定。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>互聯網分析器設計用於解決哪些方案？

互聯網分析器旨在根據您的使用者群為您提供網路性能見解。 為了説明使用者做出最佳性能決策，Internet Analyzer 使用不同的使用者群比較了兩個 Internet 終結點的性能。 雖然 Internet Analyzer 可以回答許多問題，但其中一些最常見的問題如下：

* 遷移至雲端有何效能影響？ 
    * *建議測試：自訂（當前本地基礎結構）與 Azure（任何預配置終結點）*
* 將資料放在邊緣與放在資料中心有何價值？ 
    *  *建議測試：Azure 與 Azure 前門，Azure 與來自 Microsoft 的 Azure CDN*
* Azure Front 有何效能優勢？
    *  *建議測試：自訂/Azure/CDN 與 Azure 前門*
* 來自 Microsoft 的 Azure CDN 有何效能優勢？ 
    *  *建議測試：自訂/Azure/AFD 與來自 Microsoft 的 Azure CDN*
* 來自 Microsoft 的 Azure CDN 如何堆疊？ 
    *  *建議測試：自訂（其他 CDN 終結點）與來自 Microsoft 的 Azure CDN*
* 在每個區域中何種雲端最適合您的終端使用者群體？ 
    *  *建議測試：自訂（其他雲服務）與 Azure（任何預配置終結點）*

## <a name="which-tests-can-i-run-in-preview"></a>我可以在預覽中運行哪些測試？

在 Internet 分析器中創建的每個測試由兩個終結點組成：端點 A 和終結點 B。以下任何組合都可以作為測試回合：  
* 兩個預配置的終結點，
* 一個自訂和一個預配置終結點，或
* 兩個[自訂終結點](internet-analyzer-custom-endpoint.md)（一個自訂終結點必須駐留在 Azure 中）。

預覽期間提供以下預配置終結點：
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
    * 西歐
    * 美國西部
    * 美國西部 2
* **多個 Azure 區域組合**
    * 美國東部、巴西南部
    * 美國東部、東亞
    * 西歐、巴西南部
    * 西歐、東南亞
    * 西歐、阿拉伯聯合大公國北部
    * 美國西部、美國東部
    * 美國西部、西歐
    * 美國西部、阿拉伯聯合大公國北部
    * 西歐、阿拉伯聯合大公國北部、東南亞
    * 美國西部、西歐、東亞
    * 美國西部、北歐、東南亞、阿拉伯聯合大公國北部、南非北部 
* **Azure 與 Azure Front Door** - 部署於上列任意一個或多個 Azure 區域組合上
* **Azure 與來自 Microsoft 的 Azure CDN** - 部署於上列任意一個 Azure 區域組合上
* **Azure 與 Azure 流量管理員** - 部署於上列任意多個 Azure 區域組合上

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet 分析器與 Azure 提供的其他監視服務有什麼不同？

互聯網分析器可説明您瞭解最終使用者的性能，並説明您做出決策以提高其性能。 雖然其他 Azure 監視工具提供對 Azure 服務的見解，但 Internet 分析器側重于測量使用者的端到端 Internet 性能。

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>互聯網分析儀如何處理測量資料？

Azure 有[嚴密的安全性程序，並符合各種法規遵循標準](https://azure.microsoft.com/support/trust-center/)。 只有您和您指定的小組可以存取您的資料。 Microsoft 工作人員只有在您知情的特定有限情況下，才具有其限定存取權。 它在傳輸過程中和靜態中加密。

## <a name="next-steps"></a>後續步驟

要瞭解更多資訊，請參閱我們的[互聯網分析器概述](internet-analyzer-overview.md)。
