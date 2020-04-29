---
title: 瞭解 Visual Studio App Center 和 Azure 服務的行動應用程式使用方式和使用者行為
description: 瞭解 App Center 之類的服務，以瞭解使用者如何使用您的行動應用程式，以協助您做出明智的商業決策。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241087"
---
# <a name="analyze-and-understand-mobile-application-use"></a>分析並瞭解行動應用程式的使用
您要如何瞭解使用者如何使用您的應用程式？ 您的應用程式有多少使用中的使用者，以及使用量如何隨著時間變更？ 他們使用哪些功能，以及最常使用哪些功能？ 這些使用者以何為基礎？ 有多少使用者使用最新版的應用程式？ 所有這些問題都必須瞭解，才能將您的應用程式轉變成成功的業務。 若要回答這些類型的使用方式分析問題，您必須從您的應用程式收集使用量資料。

您的資料愈多，您可能會發現改善應用程式的方式，並讓您的使用者感到滿意。 請務必使用資料來尋找可操作的深入解析，並讓使用者感到滿意。

## <a name="importance-of-analytics"></a>分析的重要性
- 瞭解您的使用者、他們如何與您的應用程式互動，以及如何讓他們回頭微調您的應用程式，並提供絕佳的體驗來拓展您的業務。
- 追蹤您的使用計量，以做出明智的決策，決定如何行銷您的應用程式，並為客戶提供更好的服務。
- 測量您的應用程式效能。
- 瞭解應用程式的哪些部分的價值和效能。
- 取得涉及變換和保留的問題資料驅動深入解析。

使用下列服務來啟用行動應用程式分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/)可讓您專注于重要事項來拓展您的物件。 它提供有關使用者會話、熱門裝置、作業系統版本和行為分析的深入報告和深入解析。 輕鬆地建立自訂事件，以追蹤任何具有大量應用程式分析的專案。

   **主要功能**
   - 免費追蹤使用模式、使用者採用和其他參與計量。
   - 透過自訂事件來識別趨勢、使用者行為和參與。
   - 針對單一儀表板中的應用程式使用量（每日、每週、每月）、會話、裝置屬性和使用者人口統計，取得現成的計量和詳細的深入解析。
   - 連續將您的所有 App Center Analytics 資料匯出至 Azure，以進行無限制的保留。 App Center 分析支援匯出至 Azure Blob 儲存體和 Azure 應用程式深入解析。
   - 與 Azure 應用程式 Insights 整合，以獲得更深入的見解，例如保留、漏斗分析和世代。
   - 使用單行 SDK 整合在幾分鐘內開始。
   - 取得適用于 iOS、Android、macOS、tvOS、Xamarin、React Native、Unity 和 Cordova 的平臺支援。

   **參考**
   - [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [開始使用 App Center 分析](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure 監視器
Azure 監視器包含[Application Insights](/azure/azure-monitor/app/app-insights-overview)，可提供工具來收集和分析遙測，以將效能最大化並監視您的行動應用程式。 您可以使用 App Center 分析來設定匯出至 Application Insights，以利用 Application Insights。 Application Insights 可以查詢、分割、篩選及分析應用程式中的自訂事件遙測，而不只是 App Center 提供的分析工具。

**主要功能**
   - 查詢您的自訂事件遙測資料。
   - 使用強大的分割功能來篩選事件遙測。
   - 分析應用程式中的轉換、保留和瀏覽模式。 您可以使用：
     - 漏斗圖以分析及監視轉換率。
     - 保留以分析您的應用程式在一段時間內保留使用者的程度。
     - 將視覺效果和文字結合成可共用報表的活頁簿。
     - 世代以命名並儲存特定的使用者或事件群組，讓他們可以輕鬆地從其他分析工具參考。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [使用 App Center 和 Application Insights 分析您的行動應用程式](/azure/azure-monitor/learn/mobile-center-quickstart)
- [搭配 Application Insights 使用 App Center 分析](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供完整的後端平臺，具備遊戲服務、即時分析和 LiveOps，您必須建立世界級的雲端連線遊戲。 這些服務可減少針對遊戲開發人員所啟動的障礙。 它們提供了大型和小型工作室符合成本效益的開發解決方案，可隨著遊戲而調整。 這些服務可協助工作室參與、留住和銷售玩家。 透過 PlayFab，開發人員可以使用智慧型雲端來建立和操作遊戲、分析遊戲資料，以及改善整體遊戲體驗。

**主要功能**
   - 監視即時儀表板。
   - 透過頂尖計量評估您的遊戲效能。
   - 透過自動產生的報表，回顧遊戲的每日和每月效能摘要。 您可以在遊戲管理員中查看報表，並每日下載或傳遞至您的收件匣。
   - 使用 A/B 測試來執行實驗，並判斷特定變數的最佳設定。
   - 使用玩家的分割來定義自動化的播放程式分組。
    
**參考**
- [PlayFab 入口網站](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入門](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
