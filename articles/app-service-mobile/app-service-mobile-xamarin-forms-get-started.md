---
title: 開始使用 Xamarin. 表單
description: 遵循此教學課程，開始使用 Mobile Apps 進行 Xamarin.Forms 開發
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461329"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>使用 Azure 建立 Xamarin.Forms 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀
本教學課程說明如何使用 Azure App Service 的 Mobile Apps 功能作為後端，將雲端型後端服務新增到 Xamarin.Forms 行動裝置應用程式。 您會同時建立新的 Mobile Apps 後端，以及可在 Azure 中儲存應用程式資料的簡易待辦事項清單 Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* 適用于 Xamarin、Visual Studio 2017 或更新版本，或 Visual Studio for Mac 的 Visual Studio Tools。 如需相關指示，請參閱[Xamarin 安裝頁面][Install Xamarin]。

* (選擇性) 若要建置 iOS 應用程式，則需要安裝了 Xcode 9.0 或更新版本的 Mac。 Visual Studio for Mac 可用來開發 iOS 應用程式，或可以使用 Visual Studio 2017 或更新版本（只要網路上有 Mac 可用）。

## <a name="create-a-new-mobile-apps-back-end"></a>建立新的 Mobile Apps 後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>執行 Xamarin Forms 解決方案

需要 Xamarin 的 Visual Studio Tools 才能開啟解決方案，請參閱[xamarin 安裝指示][Install Xamarin]。 如果工具已安裝好，請遵循下列步驟來下載並開啟解決方案：

### <a name="visual-studio-windows-and-mac"></a>Visual Studio （Windows 和 Mac）

1. 移至  [Azure 入口網站](https://portal.azure.com/)，然後流覽至您所建立的行動應用程式。 在 [`Overview`] 分頁上，尋找您的行動應用程式的公用端點 URL。 範例-應用程式名稱 "test123" 的 sitename 將會 https://test123.azurewebsites.net。

2. 開啟此資料夾中的檔案 `Constants.cs`-xamarin. forms/z。 應用程式名稱為 `ZUMOAPPNAME`。

3. 在 `Constants.cs` 類別中，將 `ZUMOAPPURL` 變數取代為上面的公用端點。

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    變成

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. 遵循下列指示來執行 Android 或 Windows 專案；如果有可用的連網 Mac 電腦，則可執行 iOS 專案。

## <a name="optional-run-the-android-project"></a>(選擇性) 執行 Android 專案

在這一節當中，您會執行 Xamarin.Android 專案。 如果未使用 Android 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android (Droid) 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取 Android 專案旁邊的 [建置] 和 [部署] 核取方塊，並確定共用程式碼專案已核取 [建置] 方塊。

4. 若要建置專案並在 Android 模擬器中啟動應用程式，請按 **F5** 鍵，或是按一下 [啟動] 按鈕。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 Android 專案，然後選取 [設為起始專案]。

2. 若要建置專案並在 Android 模擬器中啟動應用程式，請選取 [執行] 功能表，再選取 [開始偵錯]。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 ( **+** )。

![Android 待辦事項應用程式][11]

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 會存取 Mobile Apps 後端的程式碼位於解決方案共用程式碼專案的 **TodoItemManager.cs** C# 檔案中。
>

## <a name="optional-run-the-ios-project"></a>(選擇性) 執行 iOS 專案

在這一節當中，您會執行適用於 iOS 裝置的 Xamarin.iOS 專案。 如果未使用 iOS 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取 iOS 專案旁邊的 [建置] 和 [部署] 核取方塊，並確定共用程式碼專案已核取 [建置] 方塊。

4. 若要建置專案並在 iPhone 模擬器中啟動應用程式，請選取 **F5** 鍵。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]。

2. 在 [執行] 功能表上，選取 [開始偵錯] 以建置專案並在 iPhone 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 ( **+** )。

![iOS 待辦事項應用程式][10]

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 您會在解決方案共用程式碼專案的 **TodoItemManager.cs** C# 檔案中，找到會存取 Mobile Apps 後端的程式碼。
>

## <a name="optional-run-the-windows-project"></a>(選擇性) 執行 Windows 專案

在這一節當中，您會執行適用於 Window 裝置的 Xamarin.Forms 通用 Windows 平台 (UWP) 專案。 如果未使用 Windows 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下任意 UWP 專案，然後選取 [設為起始專案]。

2. 在 [建置] 功能表上，選取 [組態管理員]。

3. 在 [組態管理員] 對話方塊中，選取所選擇 Windows 專案旁邊的 [建置] 和 [部署] 核取方塊，並確定共用程式碼專案已核取 [建置] 方塊。

4. 若要建置專案並在 Windows 模擬器中啟動應用程式，請按 **F5** 鍵，或是按一下 [啟動] 按鈕 (名稱應該是**本機電腦**)。

> [!NOTE]
> Windows 專案不能在 macOS 中執行。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」)，然後選取加號 ( **+** )。

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![UWP 待辦事項應用程式][12]

> [!NOTE]
> 您將會在您方案的可攜式類別庫專案的 **TodoItemManager.cs** C# 檔案中，發現可存取 Mobile Apps 後端的程式碼。
>

## <a name="troubleshooting"></a>疑難排解

如果在建置解決方案時發生問題，請執行 NuGet 套件管理員，並更新為最新版的 `Xamarin.Forms`，並在 Android 專案中，更新 `Xamarin.Android` 支援套件。 快速入門專案不一定會包含最新版本。

請注意，您 Android 專案中所參考的所有支援套件都必須具有相同版本。 [Azure Mobile Apps NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) 具有 Android 平台的 `Xamarin.Android.Support.CustomTabs` 相依性，因此若您的專案使用較新的支援套件，您必須直接安裝具有必要版本的此套件以避免發生衝突。

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
