---
title: 包含檔案
description: 包含檔案
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440199"
---
1. 下載以下平臺的用戶端 SDK 快速入門：
    
    [iOS（目標C）](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS（Swift）](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [視窗 （C#）](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 如果您使用 iOS 專案，則需要從[最新的 GitHub 版本](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)下載"azuresdk-iOS-.zip"。\* 解壓縮檔並將`MicrosoftAzureMobile.framework`檔添加到專案的根目錄。
    >

2. 您必須添加資料庫連接或連接到現有連接。 首先，確定是創建資料存儲還是使用現有資料存儲。

    - **創建新資料存儲**：如果要創建資料存儲，請使用以下快速入門：

        [快速入門：從 Azure SQL 資料庫中的單個資料庫開始](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **現有資料來源**：如果要使用現有資料庫連接，請按照以下說明操作

        1. SQL 資料庫連接字串格式 -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **[your_SQLServer]** 伺服器的名稱，可以在資料庫的概覽頁中找到，通常以"server_name.database.windows.net"的形式顯示。
            **[埠]** 通常為 1433。
            **[your_catalogue]** 資料庫的名稱。
            **[your_username]** 訪問資料庫的使用者名。
            **[your_password]** 訪問資料庫的密碼。

            [瞭解有關 SQL 連接字串格式的更多內容](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 將連接字串添加到**移動應用**"應用服務"中，您可以使用功能表中的 **"配置"** 選項管理應用程式的連接字串。

            要添加連接字串：

            1. 按一下"**應用程式設定"** 選項卡。

            2. 按一下 **[ 新連接字串**] 。

            3. 您需要為連接字串提供**名稱**、**值**和**類型**。

            4. 將**名稱**鍵入`MS_TableConnectionString`

            5. 值應該是您在之前的步驟中形成的連接字串。

            6. 如果要向 SQL Azure 資料庫添加連接字串，請選擇**類型**下的**SQLAzure。**

3. Azure 移動應用具有用於 .NET 和 Node.js 後端的 SDK。

   - **Node.js 後端**
    
     如果要使用 Node.js 快速入門應用，請按照以下說明操作。

     1. 在 Azure 門戶中，轉到 **"簡單表**"，您將看到此螢幕。
      
        ![節點簡單表](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. 確保 SQL 連接字串已在 **"配置"** 選項卡中添加。然後選中 **"我確認這將覆蓋所有網站內容**"核取方塊，然後按一下"**創建 TodoItem"表**按鈕。
     
        ![節點簡易表配置](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. 在 **"簡單表格"** 中，按一下 **"添加"** 按鈕。
    
        ![節點簡易表添加按鈕](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 創建具有`TodoItem`匿名存取許可權的表。
      
        ![節點簡易表添加表](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 後端**
    
        如果要使用 .NET 快速入門應用，請按照以下說明操作。

        1. 從[Azure-移動應用 -快速啟動存儲庫](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)下載 Azure 移動應用 .NET 伺服器專案。

        2. 在視覺化工作室中本地生成 .NET 伺服器專案。

        3. 在視覺化工作室中，打開解決方案資源管理器，按右鍵`ZUMOAPPNAMEService`專案，按一下 **"發佈**"，您將看到一`Publish to App Service`個視窗。 如果您正在 Mac 上工作，請查看[其他方法來部署應用程式。](https://docs.microsoft.com/azure/app-service/deploy-local-git)
        
           ![視覺工作室出版](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 選擇**應用服務**作為發佈目標，然後按一下 **"選擇現有**"，然後按一下視窗底部的 **"發佈**"按鈕。

        5. 您需要首先使用 Azure 訂閱登錄到視覺化工作室。 選擇`Subscription`，`Resource Group`然後選擇應用的名稱。 準備就緒後，按一下"**確定**"，這將將本地的 .NET 伺服器專案部署到應用服務後端。 部署完成後，您將在瀏覽器中重定向到`http://{zumoappname}.azurewebsites.net/`。                   