---
title: 為物理伺服器設定 Azure 移轉裝置
description: 瞭解如何為物理伺服器評估設置 Azure 遷移設備。
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: ddc70ee9430d3a767ce01191824c150a4dbd5e6f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538268"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>設定為物理伺服器設定裝置

本文介紹,如果要使用 Azure 遷移:伺服器評估工具評估物理伺服器,如何設置 Azure 遷移設備。

Azure 移轉裝置是一種輕量級設備,Azure 遷移伺服器評估用於執行以下操作:

- 發現本地伺服器。
- 將已發現的伺服器的中資料和性能資料發送到 Azure 遷移伺服器評估。

[瞭解有關](migrate-appliance.md)Azure 遷移設備詳細資訊。


## <a name="appliance-deployment-steps"></a>裝置部署步驟

若要設定設備，請：
- 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
- 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
- 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
- 第一次設定設備，並向 Azure Migrate 專案進行註冊。

## <a name="download-the-installer-script"></a>下載安裝程式指令碼

下載設備的 ZIP 壓縮檔案。

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [未虛擬化/其他]  。
3. 按一下 [下載]**** 以下載 ZIP 壓縮檔案。

    ![下載 VM](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行以下指令以產生壓縮檔案的哈希:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公共雲的範例使用方式:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - 政府雲的範例使用方式:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  認證哈希值:
 
    - 對於公共雲(對於最新版本):

        **演算法** | **雜湊值**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - 對於 Azure 政府(對於最新的裝置版本):

        **演算法** | **雜湊值**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f


## <a name="run-the-azure-migrate-installer-script"></a>執行 Azure Migrate 安裝程式指令碼
此安裝程式指令碼會執行下列作業︰

- 安裝用於實體伺服器探索及評定的代理程式與 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 與 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續設定詳細資料來更新登錄機碼 (HKLM)。
- 在路徑底下建立下列檔案：
    - **設定檔**: %程式資料%\Microsoft Azure_Config
    - **紀錄檔**: %程式資料%\Microsoft Azure_Logs

執行指令碼，如下所示：

1. 將 ZIP 壓縮檔案解壓縮至會裝載設備之伺服器上的資料夾。  確保沒有在現有 Azure 遷移設備上的電腦上運行文稿。
2. 在上述伺服器上，使用系統管理 (提高的) 權限來啟動 PowerShell。
3. 將 PowerShell 目錄變更為已從下載的 ZIP 壓縮檔案解壓縮內容的資料夾。
4. 藉由執行下列命令，以執行名為 **AzureMigrateInstaller.ps1** 的指令碼：

    - 對於公共雲:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - 對於 Azure 政府:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    指令碼會在成功完成時啟動設備 Web 應用程式。

如果遇到任何問題,可以在 C:_程序數據\微軟 Azure_logs_AzureMigrateScenarioInstaller_<em>時間戳</em>.log 訪問腳本日誌以進行故障排除。



### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確保設備 VM 可以連接到[公共](migrate-appliance.md#public-cloud-urls)[和政府雲](migrate-appliance.md#government-cloud-urls)的 Azure URL。

## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址  :44368**。

   或者，您也可以按一下應用程式捷徑，從桌面開啟應用程式。
2. 在 [Web 應用程式] > [設定必要條件]  中，執行下列動作：
    - **授權**：接受授權條款，並閱讀第三方資訊。
    - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
        - 按一下 [Proxy 設定]  ，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，VM 探索才能正常運作。
    - **安裝更新**：Azure Migrate 伺服器評估會確認設備是否已安裝最新的更新。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]  。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
4. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
5. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
6. 按一下 [註冊]  。


## <a name="start-continuous-discovery"></a>開始連續探索

從設備連接到物理伺服器,然後啟動發現。

1. 按一下 [新增認證]**** 以指定設備會用來探索伺服器的帳戶認證。  
2. 指定**作業系統**、認證的友好名稱以及使用者名稱和密碼。 然後按一下 **[加入]**。
您可以新增一組適用於 Windows 與 Linux 伺服器的認證。
4. 按一下 [新增伺服器]****，然後指定伺服器詳細資料 - FQDN/IP 位址與認證的易記名稱 (每列一筆輸入) 以連線至伺服器。
3. 按一下 **[驗證]** 。 驗證之後，就會顯示可探索的伺服器清單。
    - 如果伺服器驗證失敗，請將滑鼠停留在 [狀態]**** 欄中的圖示上以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除伺服器，請選取 > [刪除]****。
4. 驗證之後，按一下 [儲存並開始探索]  來開始探索程序。

這會開始探索。 所探索到 VM 的中繼資料會在大約 15 分鐘後出現在 Azure 入口網站中。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

發現完成後,可以驗證伺服器是否顯示在門戶中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器   >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器  計數。


## <a name="next-steps"></a>後續步驟

試著使用 Azure 移植[伺服器評估評估物理伺服器](tutorial-assess-physical.md)。
