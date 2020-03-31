---
title: 為物理伺服器設置 Azure 遷移設備
description: 瞭解如何為物理伺服器評估設置 Azure 遷移設備。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b60a30e5e30ee81cbaca7d5e4691ccedac2462b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598165"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>為物理伺服器設置設備

本文介紹，如果要使用 Azure 遷移：伺服器評估工具評估物理伺服器，如何設置 Azure 遷移設備。

Azure 遷移設備是一種羽量級設備，Azure 遷移伺服器評估用於執行以下操作：

- 發現本機伺服器。
- 將已發現的伺服器的中繼資料和效能資料發送到 Azure 遷移伺服器評估。

[瞭解有關](migrate-appliance.md)Azure 遷移設備詳細資訊。


## <a name="appliance-deployment-steps"></a>設備部署步驟

若要設定設備，請：
- 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
- 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
- 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
- 第一次設定設備，並向 Azure Migrate 專案進行註冊。

## <a name="download-the-installer-script"></a>下載安裝程式指令碼

下載設備的 ZIP 壓縮檔案。

1. 在**遷移目標** > **中，伺服器** > **Azure 遷移：伺服器評估**，按一下 **"發現**"。
2. 在 **"發現電腦** > **"中，您的電腦是否虛擬化？，** 按一下 **"未虛擬化/其他**"。
3. 按一下 [下載]**** 以下載 ZIP 壓縮檔案。

    ![下載 VM](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 VHD 的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  對於最新的設備版本，生成的雜湊應與這些[設置](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security)匹配。



## <a name="run-the-azure-migrate-installer-script"></a>執行 Azure Migrate 安裝程式指令碼
此安裝程式指令碼會執行下列作業︰

- 安裝用於實體伺服器探索及評定的代理程式與 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 與 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續設定詳細資料來更新登錄機碼 (HKLM)。
- 在路徑底下建立下列檔案：
    - **設定檔**： %程式資料%\微軟 Azure_Config
    - **日誌檔**： %程式資料%\微軟 Azure_Logs

執行指令碼，如下所示：

1. 將 ZIP 壓縮檔案解壓縮至會裝載設備之伺服器上的資料夾。
2. 在上述伺服器上，使用系統管理 (提高的) 權限來啟動 PowerShell。
3. 將 PowerShell 目錄變更為已從下載的 ZIP 壓縮檔案解壓縮內容的資料夾。
4. 藉由執行下列命令，以執行名為 **AzureMigrateInstaller.ps1** 的指令碼：
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
指令碼會在成功完成時啟動設備 Web 應用程式。

如果發生任何問題，您可以存取位於 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>時間戳記</em>.log 的指令碼記錄，以進行疑難排解。

> [!NOTE]
> 請不要在現有的 Azure Migrate 設備上執行 Azure Migrate 安裝程式指令碼。

### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確保設備 VM 可以連接到所需的 Azure [URL。](migrate-appliance.md#url-access)

## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在可以連接到 VM 的任何電腦上打開瀏覽器，並打開設備 Web 應用的**URL：HTTPs://*設備名稱或 IP 位址*：44368**。

   或者，您也可以按一下應用程式捷徑，從桌面開啟應用程式。
2. 在 [Web 應用程式] > [設定必要條件]**** 中，執行下列動作：
    - **許可**：接受授權條款，並閱讀協力廠商資訊。
    - **連接**：應用程式檢查 VM 是否具有 Internet 存取權限。 如果 VM 使用 Proxy：
        - 按一下 [Proxy 設定]****，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：時間已驗證。 設備上的時間應該與網際網路時間同步，VM 探索才能正常運作。
    - **安裝更新**：Azure 遷移伺服器評估檢查設備是否安裝了最新的更新。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]****。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
4. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
5. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
6. 按一下 [註冊]****


## <a name="start-continuous-discovery"></a>開始連續探索

從設備連接到物理伺服器，然後啟動發現。

1. 按一下 [新增認證]**** 以指定設備會用來探索伺服器的帳戶認證。  
2. 指定**作業系統**、認證的易記名稱、**使用者名稱**與**密碼**，然後按一下 [新增]****。
您可以新增一組適用於 Windows 與 Linux 伺服器的認證。
4. 按一下 [新增伺服器]****，然後指定伺服器詳細資料 - FQDN/IP 位址與認證的易記名稱 (每列一筆輸入) 以連線至伺服器。
3. 按一下 [驗證]****。 驗證之後，就會顯示可探索的伺服器清單。
    - 如果伺服器驗證失敗，請將滑鼠停留在 [狀態]**** 欄中的圖示上以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除伺服器，請選取 > [刪除]****。
4. 驗證之後，按一下 [儲存並開始探索]**** 來開始探索程序。

這會開始探索。 所探索到 VM 的中繼資料會在大約 15 分鐘後出現在 Azure 入口網站中。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

發現完成後，可以驗證服務器是否顯示在門戶中。

1. 開啟 Azure Migrate 儀表板。
2. 在**Azure 遷移 - 伺服器** > **Azure 遷移：伺服器評估**頁中，按一下顯示**已發現伺服器**計數的圖示。


## <a name="next-steps"></a>後續步驟

嘗試使用 Azure 遷移[伺服器評估評估物理伺服器](tutorial-assess-physical.md)。
