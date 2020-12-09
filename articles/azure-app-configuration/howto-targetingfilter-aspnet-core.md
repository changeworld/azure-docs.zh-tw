---
title: 針對目標物件啟用功能的分段推出
titleSuffix: Azure App Configuration
description: 瞭解如何為目標物件啟用功能的分段推出
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929679"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>針對目標物件啟用功能的分段推出

功能旗標可讓您以動態方式啟用或停用應用程式中的功能。 功能篩選準則會決定每次評估功能旗標的狀態。 連結 `Microsoft.FeatureManagement` 庫包含 `TargetingFilter` ，可針對指定的使用者和群組清單，或指定的使用者百分比啟用功能旗標。 `TargetingFilter` 是「粘滯」。 這表示一旦個別使用者收到某項功能，就會繼續在所有未來的要求中看到該功能。 您可以使用在 `TargetingFilter` 示範期間啟用特定帳戶的功能，以漸進方式將新功能推出給不同群組的使用者或「環形」等等。

在本文中，您將瞭解如何使用搭配 Azure 應用程式組態，將 ASP.NET Core web 應用程式中的新功能推出至指定的使用者和群組 `TargetingFilter` 。

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>建立具有功能旗標和驗證的 web 應用程式

若要根據使用者和群組推出功能，您需要可讓使用者登入的 web 應用程式。

1. 使用下列命令來建立 web 應用程式，以針對本機資料庫進行驗證：

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. 建立並執行，然後選取右上角的 [ **註冊** ] 連結，以建立新的使用者帳戶。 使用的電子郵件地址 `test@contoso.com` 。 在 [ **註冊確認** ] 畫面上，選取 [ **按一下這裡以確認您的帳戶**]。

1. 遵循快速入門中的指示 [：將功能旗標新增至 ASP.NET Core 應用](./quickstart-feature-flag-aspnet-core.md) 程式，以將功能旗標新增至新的 web 應用程式。

1. 切換應用程式設定中的功能旗標。 驗證此動作可控制導覽列上 **Beta** 專案的可見度。

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>更新 web 應用程式程式碼以使用 TargetingFilter

此時，您可以使用功能旗標來啟用或停用 `Beta` 所有使用者的功能。 若要啟用某些使用者的功能旗標，並為其他使用者停用它，請更新您的程式碼來使用 `TargetingFilter` 。 在此範例中，您將使用已登入使用者的電子郵件地址做為使用者識別碼，並使用電子郵件地址的功能變數名稱部分作為群組。 您會將使用者和群組新增至 `TargetingContext` 。 會 `TargetingFilter` 使用此內容來判斷每個要求的功能旗標狀態。

1. 更新為套件的最新版本 `Microsoft.FeatureManagement.AspNetCore` 。

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. 新增 *TestTargetingCoNtextAccessor.cs* 檔案：

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. 在 *Startup.cs* 中，新增 *FeatureManagement. FeatureFilters* 命名空間的參考：

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. 更新 *ConfigureServices* 方法 `TargetingFilter` ，以在呼叫後進行註冊 `AddFeatureManagement()` ：

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. 更新 *ConfigureServices* 方法，將在 `TestTargetingContextAccessor` 先前步驟中建立的加入至服務集合。 *TargetingFilter* 會使用它來判斷每次評估功能旗標時的目標內容。

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

整個 *ConfigureServices* 方法看起來會像這樣：

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>將功能旗標更新為使用 TargetingFilter

1. 在 Azure 入口網站中，移至您的應用程式設定存放區，然後選取 [ **功能管理員**]。

1. 選取您在快速入門中建立之 *Beta* 功能旗標的內容功能表。 選取 [Edit] \(編輯\)。

    > [!div class="mx-imgBorder"]
    > ![編輯 Beta 功能旗標](./media/edit-beta-feature-flag.png)

1. 在 **編輯** 畫面中，選取 [ **啟用功能旗** 標] 核取方塊（如果尚未選取）。 然後選取 [ **使用功能篩選** ] 核取方塊。

1. 選取 [ **目標** ] 選項按鈕。

1. 選取下列選項：

    - **預設百分比**：0
    - **群組**：輸入 _contoso.com_ 的 **名稱**，並使用 _50_ 的 **百分比**
    - **使用者**： `test@contoso.com`

    [功能篩選器] 畫面看起來會像這樣：

    > [!div class="mx-imgBorder"]
    > ![條件式功能旗標](./media/feature-flag-filter-enabled.png)

    這些設定會導致下列行為：

    - 功能旗標一律為使用者啟用 `test@contoso.com` ，因為 `test@contoso.com` 列在 [ _使用者_ ] 區段中。
    - _Contoso.com_ 群組中的其他使用者可以使用功能旗標50來啟用，因為 _contoso.com_ 會列在 [群組] 區段中，而 [_群組_] 區段的 _百分比_ 為 _50_。
    - 所有其他使用者都一律停用此功能，因為 _預設百分比_ 設定為 _0_。

1. 選取 **[** 套用] 以儲存這些設定，並返回 [ **功能管理員** ] 畫面。

1. 功能旗標的 **功能篩選** 現在會顯示為 *目標*。 此狀態表示功能旗標將根據 *目標* 功能篩選準則所強制執行的條件，以每個要求為基礎啟用或停用。

## <a name="targetingfilter-in-action"></a>TargetingFilter 實際操作

若要查看此功能旗標的效果，請建立並執行應用程式。 *Beta* 專案一開始不會出現在工具列上，因為 _預設百分比_ 選項設定為0。

現在 `test@contoso.com` 使用您在註冊時所設定的密碼登入。 此搶鮮 *版（Beta* ）專案現在會出現在工具列上，因為 `test@contoso.com` 已指定為目標使用者。

下列影片將示範此行為的實際運作方式。

> [!div class="mx-imgBorder"]
> ![TargetingFilter 實際操作](./media/feature-flags-targetingfilter.gif)

您可以建立具有 `@contoso.com` 電子郵件地址的其他使用者，以查看群組設定的行為。 這些使用者的50% 將會看到 *Beta* 專案。 其他50% 不會看到 *Beta* 專案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [功能管理概觀](./concept-feature-management.md)