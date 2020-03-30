---
title: 使用 Visual Studio 應用中心和 Azure 服務瞭解移動應用程式使用方式和使用者行為
description: 瞭解應用中心等服務，這些服務通過瞭解使用者如何使用移動應用程式，説明您做出明智的業務決策。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241087"
---
# <a name="analyze-and-understand-mobile-application-use"></a>分析和理解移動應用程式的使用
您瞭解使用者如何使用您的應用程式？ 您的應用程式有多少活躍使用者，使用方式如何隨時間而變化？ 他們使用哪些功能，哪些功能使用最多？ 這些使用者位於何處？ 有多少使用者正在使用最新版本的應用程式？ 所有這些問題對於瞭解將應用轉變為成功的業務非常重要。 要回答這些類型的使用方式分析問題，您需要從應用收集使用方式資料。

您看中資料越多，就越可能找到改進應用程式和讓使用者滿意的方法。 請務必使用資料來查找可操作的見解並讓使用者滿意。

## <a name="importance-of-analytics"></a>分析的重要性
- 瞭解您的使用者、他們如何與您的應用程式交互，以及是什麼讓他們重新微調您的應用程式，並提供出色的體驗來發展您的業務。
- 跟蹤使用方式指標，就如何銷售應用程式並更好地為客戶服務做出明智的決策。
- 測量應用程式性能。
- 瞭解應用程式磁碟機價值和性能的哪些部分。
- 獲得資料驅動的洞察，瞭解與流失和保留有關的問題。

使用以下服務啟用移動應用程式分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[應用中心分析](/appcenter/analytics/)可讓您專注于重要內容，從而擴大受眾。 它提供了有關使用者會話、頂級設備、作業系統版本和行為分析的深入報告和見解。 通過廣泛的應用程式分析，輕鬆創建自訂事件來跟蹤任何內容。

   **主要功能**
   - 免費跟蹤使用模式、使用者採用和其他參與度指標。
   - 通過自訂事件確定趨勢、使用者行為和參與。
   - 在單個儀表板中獲取開箱即用的指標和有關應用程式使用方式（每日、每週、每月）、會話、裝置屬性和使用者人口統計的詳細見解。
   - 持續將所有應用中心分析資料匯出到 Azure 中，以無限制保留。 應用中心分析支援匯出到 Azure Blob 存儲和 Azure 應用程式見解。
   - 與 Azure 應用程式見解集成，獲得更深入的見解，如保留、漏斗分析和佇列。
   - 使用單行 SDK 集成在幾分鐘內即可開始。
   - 獲得對 iOS、Android、macOS、tvOS、Xamarin、反應原生、團結和科爾多瓦的平臺支援。

   **引用**
   - [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [開始應用中心分析](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure 監視器
Azure 監視器包括[應用程式見解](/azure/azure-monitor/app/app-insights-overview)，它提供了收集和分析遙測工具，以最大限度地提高性能和監視移動應用程式。 您可以使用應用中心分析設置匯出到應用程式見解，從而利用應用程式見解。 應用程式見解可以查詢、細分、篩選和分析應用程式中的自訂事件遙測，超出 App Center 提供的分析工具。

**主要功能**
   - 查詢您的自訂事件遙測資料。
   - 使用強大的分段功能篩選事件遙測。
   - 分析應用程式中的轉換、保留和導航模式。 您可以使用：
     - 用於分析和監控轉化率的漏斗。
     - 保留以分析應用程式在一段時間內保留使用者的能力。
     - 將視覺化和文本合併到可共用報表中的活頁簿。
     - 用於命名和保存特定使用者或事件組，以便可以輕鬆地從其他分析工具引用它們。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [使用應用中心和應用程式見解分析您的移動應用程式](/azure/azure-monitor/learn/mobile-center-quickstart)
- [使用應用中心分析與應用程式見解](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供了完整的後端平臺，提供遊戲服務、即時分析和 LiveOps，您需要創建世界一流的雲連接遊戲。 這些服務減少了遊戲開發人員的啟動障礙。 他們提供大型和小型工作室經濟高效的開發解決方案，可隨遊戲擴展規模。 這些服務可以説明工作室吸引、保留和獲利。 借助 PlayFab，開發人員可以使用智慧雲構建和操作遊戲、分析遊戲資料並改善整體遊戲體驗。

**主要功能**
   - 監控即時儀表板。
   - 通過頂級指標評估遊戲的性能。
   - 通過自動生成的報告查看遊戲每日和每月性能摘要。 您可以在遊戲管理器中查看報告，並每天下載或將其發送到您的收件匣。
   - 使用 A/B 測試回合實驗並確定特定變數的最佳設置。
   - 使用玩家細分定義玩家的自動分組。
    
**引用**
- [PlayFab 門戶](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入門](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
