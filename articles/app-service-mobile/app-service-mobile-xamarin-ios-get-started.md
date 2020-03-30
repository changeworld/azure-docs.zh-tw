---
title: 開始使用 Xamarin.iOS 應用
description: 遵循本教學課程，開始使用 Mobile Apps 進行 Xamarin.iOS 開發。
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458997"
---
# <a name="create-a-xamarinios-app"></a>建立 Xamarin.iOS 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>總覽
本教學課程說明如何使用 Azure 行動 app 後端，將雲端型後端服務加入至 Xamarin.iOS 行動 app。  您會建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」** Xaamrin.iOS 應用程式。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

## <a name="prerequisites"></a>Prerequisites
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio for Mac。 請參閱[Mac 視覺化工作室的設置和安裝](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* 具有 Xcode 9.0 或更高版本的 Mac。
  
## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>創建資料庫連接並配置用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>運行 Xamarin.iOS 應用
1. 打開 Xamarin.iOS 專案。

2. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

3. 打開此資料夾中`QSTodoService.cs`的檔 - xamarin.iOS/ZUMOAPPNAME。 應用程式名稱是 `ZUMOAPPNAME`。

4. 在課堂上`QSTodoService`，將變數`ZUMOAPPURL`替換為上面的公共終結點。

    `const string applicationURL = @"ZUMOAPPURL";`

    變成
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 鍵在 iPhone 模擬器中部署和運行應用程式。

6. 在應用中，鍵入有意義的文本，如*完成教程，* 然後按一下 + 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

   > [!NOTE]
   > 您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
