---
title: 開始使用 Xamarin.安卓應用程式
description: 請按照本教程開始使用 Azure 移動應用進行 Xamarin Android 開發。
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461363"
---
# <a name="create-a-xamarinandroid-app"></a>建立 Xamarin.Android 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>總覽
本教學課程將示範如何將雲端後端服務加入至 Xamarin.Android 應用程式。 如需詳細資訊，請參閱 [什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。

以下是完成之應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>Prerequisites
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的 Mobile Apps。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio 和 Xamarin。 如需相關指示，請參閱 [設定和安裝 Visual Studio 和 Xamarin](/visualstudio/cross-platform/setup-and-install) 。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
依照下列步驟建立行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>創建資料庫連接並配置用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>運行 Xamarin.安卓應用程式
1. 打開 Xamarin.安卓專案。

2. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

3. 打開此資料夾中`ToDoActivity.cs`的檔 - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs。 應用程式名稱是 `ZUMOAPPNAME`。

4. 在課堂上`ToDoActivity`，將變數`ZUMOAPPURL`替換為上面的公共終結點。

    `const string applicationURL = @"ZUMOAPPURL";`

    變成
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 鍵部署和運行應用。

6. 在應用程式中輸入有意義的文字 (例如 Complete the tutorial**)，然後按一下 [新增]**** 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

   > [!NOTE]
   > 您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。
   
## <a name="troubleshooting"></a>疑難排解
如果在建置解決方案時發生問題，請執行 NuGet 套件管理員，並更新 `Xamarin.Android` 支援套件。 快速入門專案不一定會包含最新版本。

請注意，您專案中參考的所有支援套件都必須有相同的版本。 [Azure Mobile Apps NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) 具有 Android 平台的 `Xamarin.Android.Support.CustomTabs` 相依性，因此若您的專案使用較新的支援套件，您必須直接安裝具有必要版本的此套件以避免發生衝突。

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
