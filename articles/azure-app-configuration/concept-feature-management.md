---
title: 使用 Azure 應用配置瞭解功能管理
description: 使用 Azure 應用配置打開和關閉功能
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523725"
---
# <a name="feature-management-overview"></a>功能管理概觀

傳統上，如果要傳送新的應用程式功能，就需要完整重新部署應用程式本身。 測試功能通常需要應用程式的多個部署。  每個部署可能會更改功能或向不同的客戶公開該功能以進行測試。  

功能管理是現代的軟體開發實務，可讓功能發行與程式碼部署脫勾，而能夠快速地依需求變更功能可用性。 它使用一種名為「功能旗標」**(也稱為「功能切換」**、「功能開關」** 等等) 的技術來動態管理功能的生命週期。

功能管理可説明開發人員解決以下問題：

* **代碼分支管理**：使用功能標誌包裝當前開發的新應用程式功能。 這類功能預設會「隱藏」起來。 您可以安全地傳送功能 (即使尚未完成)，讓其在生產環境中保持隱匿狀態。 使用這個稱為「隱匿部署」** 的方法，您便可以在每個開發週期結束時發行所有程式碼。 您不再需要跨多個開發週期維護代碼分支，因為給定功能需要多個週期才能完成。
* **在生產中測試**：使用功能標誌允許早期訪問生產中的新功能。 例如，您可以限制對團隊成員或內部 Beta 測試人員的訪問。 這些使用者將在測試環境中體驗全保真生產體驗，而不是類比或部分體驗。
* **飛行**：使用功能標誌向最終使用者逐步推出新功能。 您可以首先定位一小部分使用者群，並隨著時間的推移逐漸增加該百分比。
* **即時殺傷開關**：功能標誌為發佈新功能提供了一個固有的安全網。 無需重新部署任何代碼即可打開和關閉應用程式功能。 如有必要，您可以快速停用功能，而無須重建和重新部署應用程式。
* **選擇性啟動**：使用功能標誌對使用者進行分段，並將一組特定的功能交付給每個組。 您可能具有僅在特定 Web 瀏覽器上工作的功能。 您可以定義功能旗標，從而只讓該瀏覽器的使用者看見並使用該功能。 藉由此方法，您可以在之後輕鬆地擴充支援的瀏覽器清單，而不必變更程式碼。

## <a name="basic-concepts"></a>基本概念

以下是數個與功能管理有關的新詞彙：

* **特徵標誌**：要素標誌是二進位狀態為*開**或關*的變數。 功能旗標也有相關聯的程式碼區塊。 要素標誌的狀態觸發代碼塊是否運行。
* **功能管理器**：功能管理器是處理應用程式中所有功能標誌的生命週期的應用程式包。 功能管理器還提供其他功能，包括緩存功能標誌並更新其狀態。
* **篩選器**：篩選器是用於評估要素標誌狀態的規則。 潛在篩選器包括使用者組、設備或瀏覽器類型、地理位置和時間視窗。

功能管理的有效實作至少須由兩個搭配運作的元件組成：

* 一個使用功能旗標的應用程式。
* 一個儲存功能旗標及其目前狀態的個別存放庫。

## <a name="using-feature-flags-in-your-code"></a>在代碼中使用功能標誌

在應用程式中實作功能旗標的基本模式很簡單。 特徵標誌是控制代碼中條件陳述式的布林狀態變數：

```csharp
if (featureFlag) {
    // Run the following code
}
```

您可以靜態設置`featureFlag`值。

```csharp
bool featureFlag = true;
```

您可以根據某些規則評估標誌的狀態：

```csharp
bool featureFlag = isBetaUser();
```

您可以擴展條件以為任一狀態設置應用程式行為：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能旗標存放庫

若要有效地使用功能旗標，您需要將應用程式中使用的所有功能旗標放在外部。 這允許您更改功能標誌狀態，而無需修改和重新部署應用程式本身。

Azure 應用配置為功能標誌提供了集中式存儲庫。 您可以使用 Azure 應用程式組態來定義不同類型的功能旗標，並放心地快速操作其狀態。 接著，您可以使用適用於各種程式設計語言架構的應用程式組態程式庫，輕鬆地從應用程式存取這些功能旗標。

[在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)說明如何搭配使用 .NET Core 應用程式組態提供者和功能管理程式庫，為您的 ASP.NET Web 應用程式實作功能旗標。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將功能旗標新增至 ASP.NET Core Web 應用程式](./quickstart-feature-flag-aspnet-core.md)  
