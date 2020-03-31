---
title: 建立 UWP 應用程式
description: 遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、Visual Basic 或 JavaScript 的通用 Windows 平台 (UWP) 應用程式開發。
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461380"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>建立具有 Azure 後端的 Windows 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>總覽

本教學課程顯示如何將雲端型後端服務新增到通用 Windows 平台 (UWP) 應用程式。 如需詳細資訊，請參閱 [什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。 以下是已完成之應用程式的螢幕截圖：

![完成的電腦桌面應用程式](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

完成本教學課程是 UWP 應用程式的所有其他行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Windows 10。
* 視覺工作室社區2017年。
* 熟悉如何開發 UWP 應用程式。 請瀏覽 [UWP 文件](https://docs.microsoft.com/windows/uwp/)，以了解如何[完成設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)來建置 UWP 應用程式。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>創建資料庫連接並配置用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>運行用戶端專案

1. 打開 UWP 專案。

2. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

3. 打開此資料夾中`App.xaml.cs`的檔 - 視窗-uwp-cs/ZUMOAPPNAME/。 應用程式名稱是 `ZUMOAPPNAME`。

4. 在課堂上`App`，將參數`ZUMOAPPURL`替換為上面的公共終結點。

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    變成
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. 按 F5 鍵部署和運行應用。

6. 在應用程式的 [插入 TodoItem]**** 文字方塊中輸入有意義的文字 (例如「完成教學課程」(Complete the tutorial)」**)，然後按一下 [儲存]****。

    ![Windows 快速入門完整桌面](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。
