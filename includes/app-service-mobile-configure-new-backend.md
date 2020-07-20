---
title: 包含檔案
description: 包含檔案
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461822"
---
1. 下載以下平台的用戶端 SDK 快速入門:
    
    [iOS(目標C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS(Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [視窗 (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 如果您使用 iOS 專案,則需要從[最新的 GitHub 版本](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)下載"azuresdk-iOS-.zip"。\* 解壓縮檔案並將`MicrosoftAzureMobile.framework`檔案添加到專案的根目錄。
    >

2. 您必須添加資料庫連接或連接到現有連接。 首先,確定是創建數據存儲還是使用現有數據存儲。

    - **建立新資料儲存**:如果要建立資料儲存,請使用以下快速入門:

        [快速入門:從 Azure SQL 資料庫中的單個資料庫開始](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **現有資料來源**:如果要使用現有資料庫連接,請按照以下說明操作

        1. SQL 資料庫連線字串格式 -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **[your_SQLServer]** 伺服器的名稱,可以在資料庫的概覽頁中找到,通常以"server_name.database.windows.net"的形式顯示。
            **[埠]** 通常為 1433。
            **[your_catalogue]** 資料庫的名稱。
            **[your_username]** 訪問資料庫的使用者名稱。
            **[your_password]** 訪問資料庫的密碼。

            [瞭解此 SQL 連接字串格式的更多內容](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 將連接字串新增到**移動應用**「應用服務」中,您可以使用選單中的 **「設定」** 選項管理應用程式的連接字串。

            要新增連線字串:

            1. 按下應用程式**設定「** 選項卡」。

            2. 按下 **( 新連線 )**。

            3. 您必須為連接字串提供**名稱**、**值**與**類型**。

            4. 將**名稱**鍵入`MS_TableConnectionString`

            5. 值應該是您在之前的步驟中形成的連接字串。

            6. 如果要向 SQL Azure 資料庫添加連接字串,請選擇**類型**下的**SQLAzure。**

3. Azure 行動應用程式具有用於 .NET 和 Node.js 後端的 SDK。

   - **Node.js 後端**
    
     如果要使用 Node.js 快速入門應用,請按照以下說明操作。
     
        1. 建立新 API - 您可以在 Azure 門戶中直接進行更改,或在開發環境中在本地修改代碼,然後發布到 Azure。 按下下面`Development Tools`選`App Service Editor (Preview)`單 ,該功能表為您的應用程式碼提供瀏覽器內編輯體驗。
        
        2. 按一下`Go`, 應用服務編輯器開啟後,您可以完全控制原始碼。 假設您已經安裝了帶有 npm 安裝命令的 Express 和 azure-行動應用程式,請按下 WWWROOT 下的 api 資料夾以建立或編輯自訂 API。 對代碼檔進行更改,並自動儲存更改。
        
        3. 您可以完全控制用於儲存應用程式資料的 Azure SQL 資料庫。 您可以輕鬆地在資料庫中創建新錶。
 
   - **.NET 後端**
    
        如果要使用 .NET 快速入門應用,請按照以下說明操作。

        1. 從[Azure-行動應用 -快速啟動儲存庫](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)下載 Azure 行動應用 .NET 伺服器專案。

        2. 在可視化工作室中本地生成 .NET 伺服器專案。

        3. 在視覺化工作室中,打開解決方案資源管理員,右鍵單擊`ZUMOAPPNAMEService`專案,按下 **「發布」,** 您將看到一`Publish to App Service`個視窗。 如果您正在 Mac 上工作,請查看[其他方法來部署應用程式。](https://docs.microsoft.com/azure/app-service/deploy-local-git)
        
           ![視覺工作室出版](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 選擇**應用服務**作為發佈目標,然後按下 **「選擇現有**」,然後單擊視窗底部的 **「發布**」按鈕。

        5. 您需要首先使用 Azure 訂閱登錄到可視化工作室。 選擇`Subscription`,`Resource Group`然後選擇應用的名稱。 準備就緒後,按一下「**確定**」,這將將本地的 .NET 伺服器專案部署到應用服務後端。 部署完成後,您將在瀏覽器中重定到`http://{zumoappname}.azurewebsites.net/`。                   
