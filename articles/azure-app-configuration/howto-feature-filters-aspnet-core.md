---
title: 使用功能篩選器為使用者子集啟用功能
titleSuffix: Azure App Configuration
description: 瞭解如何使用功能篩選器為使用者子集啟用功能
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/9/2020
ms.author: lcozzens
ms.openlocfilehash: b01a22d5a7068ee49e718a66432f52a8f6ef02ac
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126446"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>使用功能篩選器為使用者子集啟用功能

功能旗標可讓您在應用程式中啟用或停用功能。 簡單的功能旗標為開啟或關閉。 應用程式一律會以相同的方式運作。 例如，您可以在功能旗標後方推出新功能。 啟用功能旗標時，所有使用者都會看到新功能。 停用功能旗標會隱藏新的功能。

相反地，_條件式功能旗_標可讓您以動態方式啟用或停用功能旗標。 根據功能旗標準則，應用程式可能會有不同的行為。 假設您想要一開始就為一小部分的使用者顯示您的新功能。 條件式功能旗標可讓您啟用某些使用者的功能旗標，並將其停用給其他使用者。 _功能篩選準則_會在每次評估時決定功能旗標的狀態。

`Microsoft.FeatureManagement` 程式庫包含兩個功能篩選準則：

- `PercentageFilter` 會根據百分比啟用功能旗標。
- `TimeWindowFilter` 在指定的時間範圍內啟用功能旗標。

您也可以建立自己的功能篩選器來執行[FeatureManagement IFeatureFilter 介面](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>註冊功能篩選

您可以藉由呼叫 `AddFeatureFilter` 方法來註冊功能篩選，指定功能篩選器的名稱。 例如，下列程式碼會註冊 `PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 應用程式組態中設定功能篩選

某些功能篩選器具有其他設定。 例如，`PercentageFilter` 會根據百分比來啟動功能。 它有一個設定可定義要使用的百分比。

您可以針對 Azure 應用程式組態中定義的功能旗標，設定這些設定。 例如，請遵循下列步驟，使用 `PercentageFilter` 來啟用對 web 應用程式提出50% 要求的功能旗標：

1. 遵循[快速入門：將功能旗標新增至 ASP.NET Core 應用程式](./quickstart-feature-flag-aspnet-core.md)中的指示，建立具有功能旗標的 web 應用程式。

1. 在 Azure 入口網站中，移至您的設定存放區，然後按一下 **功能管理員**。

1. 針對您在快速入門中建立的搶鮮*版（Beta* ）功能旗標，按一下內容功能表。 按一下 **[編輯]** 。

    > [!div class="mx-imgBorder"]
    > ![編輯搶鮮版（Beta）功能旗標](./media/edit-beta-feature-flag.png)

1. 在 [**編輯**] 畫面中，選取 [**開啟**] 選項按鈕（如果尚未選取）。 然後按一下 [**新增篩選**] 按鈕。 （[**開啟**] 選項按鈕的標籤會變更為 [讀取**條件**]）。

1. 在 [**金鑰**] 欄位中，輸入 [ *Microsoft. 百分比*]。

    > [!div class="mx-imgBorder"]
    > ![新增功能篩選](./media/feature-flag-add-filter.png)

1. 按一下功能篩選鍵旁的內容功能表。 按一下 [**編輯參數**]。

    > [!div class="mx-imgBorder"]
    > ![編輯功能篩選參數](./media/feature-flag-edit-filter-parameters.png)

1. 將滑鼠停留在 [**名稱**] 標頭下方，讓文字方塊出現在方格中。 輸入*值*的**名稱**和**值**50。 [**值**] 欄位表示要啟用功能篩選的要求百分比。

    > [!div class="mx-imgBorder"]
    > ![設定功能篩選參數](./media/feature-flag-set-filter-parameters.png)

1. 按一下 **[** 套用] 以返回 [**編輯功能旗**標] 畫面。 然後再**按一下 [** 套用] 以儲存功能旗標設定。

1. 功能旗標的**狀態**現在會顯示為 [*條件*式]。 此狀態表示功能旗標會根據功能篩選器強制執行的準則，以每個要求為基礎來啟用或停用。

    > [!div class="mx-imgBorder"]
    > ![條件式功能旗標](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>功能篩選作用中

若要查看此功能旗標的效果，請啟動應用程式，並多次點擊瀏覽器中**的 [重新**整理] 按鈕。 您會看到*Beta*專案出現在工具列上，大約有50% 的時間。 因為 `PercentageFilter` 會停用要求子集的*Beta 版*功能，所以它會在其餘時間隱藏。 下列影片顯示此行為的實際運作方式。

> [!div class="mx-imgBorder"]
> ![PercentageFilter in action](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [功能管理總覽](./concept-feature-management.md)