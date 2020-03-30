---
title: 使用功能篩選器為使用者子集啟用功能
titleSuffix: Azure App Configuration
description: 瞭解如何使用功能篩選器為使用者子集啟用功能
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057005"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>使用功能篩選器為使用者子集啟用功能

功能標誌允許您啟動或停用應用程式中的功能。 簡單的功能標誌是打開或關閉的。 應用程式始終以相同的方式執行。 例如，您可以在要素標誌後面推出一個新功能。 啟用功能標誌後，所有使用者都會看到新功能。 禁用功能標誌將隱藏新功能。

相反，_條件要素標誌_允許動態啟用或禁用要素標誌。 應用程式的行為可能不同，具體取決於功能標誌條件。 假設您首先要向一小部分使用者顯示您的新功能。 條件要素標誌允許您為某些使用者啟用功能標誌，同時為其他使用者禁用它。 _要素篩選器_在每次計算要素標誌時確定其狀態。

該`Microsoft.FeatureManagement`庫包括兩個功能篩選器：

- `PercentageFilter`基於百分比啟用要素標誌。
- `TimeWindowFilter`在指定的時間視窗內啟用要素標誌。

您還可以創建自己的功能篩選器來實現[Microsoft.功能管理.I功能篩選器介面](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)。

## <a name="registering-a-feature-filter"></a>註冊功能篩選器

通過調用`AddFeatureFilter`方法來註冊要素篩選器，指定要素篩選器的名稱。 例如，以下代碼註冊`PercentageFilter`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>在 Azure 應用配置中配置功能篩選器

某些功能篩選器具有其他設置。 例如，`PercentageFilter`根據百分比啟動要素。 它有一個定義要使用的百分比的設置。

您可以為 Azure 應用配置中定義的功能標誌配置這些設置。 例如，按照以下步驟對`PercentageFilter`Web 應用的 50% 請求啟用功能標誌：

1. 按照["快速入門"中的說明操作：將功能標誌添加到ASP.NET核心應用](./quickstart-feature-flag-aspnet-core.md)以創建具有功能標誌的 Web 應用。

1. 在 Azure 門戶中，轉到配置存儲並按一下 **"功能管理器**"。

1. 按一下在快速入門中創建的*Beta*功能標誌的內容功能表。 按一下 **[編輯]**。

    > [!div class="mx-imgBorder"]
    > ![編輯測試版功能標誌](./media/edit-beta-feature-flag.png)

1. 在 **"編輯"** 螢幕中，如果尚未選中"**打開**"按鈕，請選擇該按鈕。 然後按一下"**添加篩選器"** 按鈕。 （"**打開**單選"按鈕的標籤將更改為"**條件**"。

1. 在 **"鍵"** 欄位中，輸入*Microsoft.百分比*。

    > [!div class="mx-imgBorder"]
    > ![添加功能篩選器](./media/feature-flag-add-filter.png)

1. 按一下要素篩選器鍵旁邊的內容功能表。 按一下 **"編輯參數**"。

    > [!div class="mx-imgBorder"]
    > ![編輯要素篩選器參數](./media/feature-flag-edit-filter-parameters.png)

1. 將滑鼠懸停在 **"名稱"** 標題下，以便文字方塊顯示在網格中。 輸入值**名稱**和*Value***值**50。 "**值**"欄位指示啟用要素篩選器的請求的百分比。

    > [!div class="mx-imgBorder"]
    > ![設置功能篩選器參數](./media/feature-flag-set-filter-parameters.png)

1. 按一下 **"應用"** 以返回到 **"編輯要素"標誌**螢幕。 然後按一下"**再次應用**"以保存功能標誌設置。

1. 要素標誌**的狀態**現在顯示為*條件*。 此狀態表示功能標誌將根據功能篩選器強制執行的條件根據請求啟用或禁用。

    > [!div class="mx-imgBorder"]
    > ![條件要素標誌](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>功能篩選器在操作中

要查看此功能標誌的效果，請啟動應用程式並多次點擊瀏覽器中的 **"刷新**"按鈕。 您將看到*Beta*項大約 50% 的時間顯示在工具列上。 它隱藏了其餘時間，因為`PercentageFilter`停用了請求子集的*Beta*功能。 以下視頻顯示此行為在起作用。

> [!div class="mx-imgBorder"]
> ![操作中的百分比篩選](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [功能管理概觀](./concept-feature-management.md)
