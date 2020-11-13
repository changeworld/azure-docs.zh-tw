---
title: 使用功能篩選來啟用條件式功能旗標
titleSuffix: Azure App Configuration
description: 瞭解如何使用功能篩選來啟用條件式功能旗標
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: af8df66e02dc9316311f36dec60374a7c4e649b8
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554733"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>使用功能篩選來啟用條件式功能旗標

功能旗標可讓您啟用或停用應用程式中的功能。 簡單的功能旗標為開啟或關閉。 應用程式一律會以相同方式運作。 例如，您可以在功能旗標後方推出新功能。 啟用功能旗標時，所有使用者都會看到新的功能。 停用功能旗標會隱藏新的功能。

相反地， _條件式功能旗_ 標可讓您以動態方式啟用或停用功能旗標。 視功能旗標準則而定，應用程式可能會有不同的行為。 假設您想要在一開始就將新功能顯示給使用者的一小部分。 條件式功能旗標可讓您啟用某些使用者的功能旗標，並為其他使用者停用它。 _功能篩選準則_ 會決定每次評估功能旗標的狀態。

此連結 `Microsoft.FeatureManagement` 庫包含三個功能篩選：

- `PercentageFilter` 根據百分比啟用功能旗標。
- `TimeWindowFilter` 在指定的時間範圍內啟用功能旗標。
- `TargetingFilter` 啟用指定使用者和群組的功能旗標。

您也可以建立自己的功能篩選器來執行 [FeatureManagement IFeatureFilter 介面](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>註冊功能篩選

您可以藉由呼叫方法來註冊功能篩選器 `AddFeatureFilter` ，並指定功能篩選器的名稱。 例如，下列程式碼會註冊 `PercentageFilter` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 應用程式組態中設定功能篩選

某些功能篩選器有其他設定。 例如，會 `PercentageFilter` 根據百分比來啟用功能。 它有一個設定，可定義要使用的百分比。

您可以針對 Azure 應用程式組態中定義的功能旗標設定這些設定。 例如，請遵循下列步驟來 `PercentageFilter` 啟用將50% 的要求傳送至 web 應用程式的功能旗標：

1. 遵循快速入門中的指示 [：將功能旗標新增至 ASP.NET Core 應用程式](./quickstart-feature-flag-aspnet-core.md) ，以使用功能旗標來建立 web 應用程式。

1. 在 Azure 入口網站中，移至您的設定存放區，然後按一下 [ **功能管理員** ]。

1. 按一下您在快速入門中建立之 *Beta* 功能旗標的內容功能表。 按一下 **[編輯]** 。

    > [!div class="mx-imgBorder"]
    > ![編輯 Beta 功能旗標](./media/edit-beta-feature-flag.png)

1. 在 **編輯** 畫面中，選取 [ **開啟** ] 選項按鈕（如果尚未選取）。 然後按一下 [ **加入篩選** ] 按鈕。  ( **On** 選項按鈕的標籤會變更為 [讀取 **條件** ]。 ) 

1. 在 [索引 **鍵** ] 欄位中，輸入 [ *Microsoft. 百分比* ]。

    > [!div class="mx-imgBorder"]
    > ![新增功能篩選](./media/feature-flag-add-filter.png)

1. 按一下功能篩選索引鍵旁的內容功能表。 按一下 [ **編輯參數** ]。

    > [!div class="mx-imgBorder"]
    > ![編輯功能篩選參數](./media/feature-flag-edit-filter-parameters.png)

1. 將滑鼠停留在 **名稱** 標頭底下，讓文字方塊出現在方格中。 輸入 *值* 的 **名稱** ，並輸入50的 **值** 。 [ **值** ] 欄位表示要啟用功能篩選的要求百分比。

    > [!div class="mx-imgBorder"]
    > ![設定功能篩選參數](./media/feature-flag-set-filter-parameters.png)

1. 按一下 **[** 套用] 返回 **編輯功能旗** 標畫面。 然後按一下 **[** 套用] 以儲存功能旗標設定。

1. 功能旗標的 **狀態** 現在會顯示為 [ *條件* ]。 此狀態表示根據功能篩選準則所強制執行的條件，會根據每個要求來啟用或停用功能旗標。

    > [!div class="mx-imgBorder"]
    > ![條件式功能旗標](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>作用中的功能篩選

若要查看此功能旗標的效果，請啟動應用程式， **並在流覽** 器中多次點擊 [重新整理] 按鈕。 您會看到 *Beta* 專案出現在工具列上大約50% 的時間。 因為會 `PercentageFilter` 停用要求子集的 *Beta* 功能，所以它會在其餘的時間隱藏起來。 下列影片將示範此行為的實際運作方式。

> [!div class="mx-imgBorder"]
> ![TargetingFilter 實際操作](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [針對目標物件啟用功能的分段推出](./howto-targetingfilter-aspnet-core.md)
