---
title: 瞭解使用 Azure 應用程式組態的功能管理
description: 使用 Azure 應用程式組態開啟和關閉功能
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b77f0063b37adbfaecaff68387e858d0077561b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212658"
---
# <a name="feature-management-overview"></a>功能管理概觀

傳統上，如果要傳送新的應用程式功能，就需要完整重新部署應用程式本身。 測試功能通常需要多個應用程式部署。  每個部署可能會變更功能，或將功能公開給不同的客戶進行測試。  

功能管理是現代的軟體開發實務，可讓功能發行與程式碼部署脫勾，而能夠快速地依需求變更功能可用性。 它使用一種名為「功能旗標」**(也稱為「功能切換」**、「功能開關」** 等等) 的技術來動態管理功能的生命週期。

功能管理可協助開發人員解決下列問題：

* 程式**代碼分支管理**：使用功能旗標包裝目前正在開發的新應用程式功能。 這類功能預設會「隱藏」起來。 您可以安全地傳送功能 (即使尚未完成)，讓其在生產環境中保持隱匿狀態。 使用這個稱為「隱匿部署」** 的方法，您便可以在每個開發週期結束時發行所有程式碼。 您不再需要跨多個開發週期維護程式碼分支，因為指定的功能需要一個以上的迴圈才能完成。
* **在生產環境中測試**：使用功能旗標來授與早期存取生產環境中的新功能。 例如，您可以限制對小組成員或內部 Beta 測試人員的存取。 這些使用者將會遇到全精確度的生產體驗，而不是測試環境中的模擬或部分體驗。
* **試驗**：使用功能旗標，以累加方式將新功能推出給終端使用者。 您可以先將一小部分的使用者人口設為目標，並在一段時間後逐漸增加該百分比。
* **立即終止切換**：功能旗標提供了用來釋出新功能的固有安全網路。 您可以開啟和關閉應用程式功能，而不需重新部署任何程式碼。 如有必要，您可以快速停用功能，而無須重建和重新部署應用程式。
* **選擇性啟用**：使用功能旗標來區隔使用者，並將一組特定功能傳遞至每個群組。 您的功能可能只適用于特定的網頁瀏覽器。 您可以定義功能旗標，從而只讓該瀏覽器的使用者看見並使用該功能。 藉由此方法，您可以在之後輕鬆地擴充支援的瀏覽器清單，而不必變更程式碼。

## <a name="basic-concepts"></a>基本概念

以下是數個與功能管理有關的新詞彙：

* **功能旗**標：功能旗標是具有 *on* 或 *off*之二進位狀態的變數。 功能旗標也有相關聯的程式碼區塊。 功能旗標的狀態會觸發程式碼區塊是否執行。
* **功能**管理員：功能管理員是應用程式套件，可處理應用程式中所有功能旗標的生命週期。 功能管理員也提供額外的功能，包括快取功能旗標和更新其狀態。
* **篩選**：篩選準則是用來評估功能旗標狀態的規則。 可能的篩選包括使用者群組、裝置或瀏覽器類型、地理位置和時間 windows。

功能管理的有效實作至少須由兩個搭配運作的元件組成：

* 一個使用功能旗標的應用程式。
* 一個儲存功能旗標及其目前狀態的個別存放庫。

## <a name="using-feature-flags-in-your-code"></a>在程式碼中使用功能旗標

在應用程式中實作功能旗標的基本模式很簡單。 功能旗標是在您的程式碼中控制條件陳述式的布林值狀態變數：

```csharp
if (featureFlag) {
    // Run the following code
}
```

您可以設定靜態的值 `featureFlag` 。

```csharp
bool featureFlag = true;
```

您可以根據特定規則來評估旗標的狀態：

```csharp
bool featureFlag = isBetaUser();
```

您可以擴充條件來設定任一狀態的應用程式行為：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能旗標存放庫

若要有效地使用功能旗標，您需要將應用程式中使用的所有功能旗標放在外部。 這可讓您變更功能旗標狀態，而不需要修改和重新部署應用程式本身。

Azure 應用程式組態提供功能旗標的集中式存放庫。 您可以使用 Azure 應用程式組態來定義不同類型的功能旗標，並放心地快速操作其狀態。 接著，您可以使用適用於各種程式設計語言架構的應用程式組態程式庫，輕鬆地從應用程式存取這些功能旗標。

[在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)說明如何搭配使用 .NET Core 應用程式組態提供者和功能管理程式庫，為您的 ASP.NET Web 應用程式實作功能旗標。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將功能旗標新增至 ASP.NET Core Web 應用程式](./quickstart-feature-flag-aspnet-core.md)  
