---
title: 設定實體伺服器的 Azure Migrate 設備
description: 瞭解如何設定 Azure Migrate 設備以進行實體伺服器評量。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/15/2020
ms.openlocfilehash: 73c3d529978c91946632ed599f02b8938830621e
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705314"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>設定實體伺服器的設備

本文說明如果您要使用 Azure Migrate：伺服器評估工具來評估實體伺服器，如何設定 Azure Migrate 設備。

Azure Migrate 設備是輕量型設備，可供 Azure Migrate 伺服器評量用來執行下列動作：

- 探索內部部署伺服器。
- 將探索到的伺服器的中繼資料和效能資料傳送至 Azure Migrate 伺服器評量。

[深入了解](migrate-appliance.md) Azure Migrate 設備的相關資訊。


## <a name="appliance-deployment-steps"></a>設備部署步驟

若要設定設備，請：
- 提供設備名稱，並在入口網站中產生 Azure Migrate 專案金鑰。
- 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
- 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
- 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
- 進行設備的第一次設定，並使用 Azure Migrate 專案金鑰將其註冊至 Azure Migrate 專案。

### <a name="generate-the-azure-migrate-project-key"></a>產生 Azure Migrate 專案金鑰

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 **探索電腦** > **電腦是否已虛擬化？** 中，選取 [實體或其他 (AWS、GCP、Xen 等。)]。
3. 在 **1：產生 Azure Migrate 專案金鑰** 中，為您要為探索之實體或虛擬伺服器設定的 Azure Migrate 設備命名。名稱應使用英數位元，且長度不超過 14 個字元。
1. 按一下 [產生金鑰] ，開始建立必要的 Azure 資源。 在建立資源期間，請勿關閉探索的電腦頁面。
1. 成功建立 Azure 資源之後，系統會產生 **Azure Migrate 專案金鑰**。
1. 複製金鑰，您在設定期間需要此金鑰才能完成設備的註冊。

### <a name="download-the-installer-script"></a>下載安裝程式指令碼

在 **2：下載 Azure Migrate 設備** 中，按一下 [下載]。

   ![探索電腦的選取項目](./media/tutorial-assess-physical/servers-discover.png)


   ![產生金鑰的選取項目](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公用雲端的使用範例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 政府雲端的使用範例：```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  確認設備的最新版本，以及 [雜湊值](tutorial-discover-physical.md#verify-security) 設定。
 

## <a name="run-the-azure-migrate-installer-script"></a>執行 Azure Migrate 安裝程式指令碼
此安裝程式指令碼會執行下列作業︰

- 安裝用於實體伺服器探索及評定的代理程式與 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 與 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續設定詳細資料來更新登錄機碼 (HKLM)。
- 在路徑底下建立下列檔案：
    - **組態檔**：%Programdata%\Microsoft Azure\Config
    - **記錄檔**：%Programdata%\Microsoft Azure\Logs

執行指令碼，如下所示：

1. 將 ZIP 壓縮檔案解壓縮至會裝載設備之伺服器上的資料夾。  請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在上述伺服器上，使用系統管理 (提高的) 權限來啟動 PowerShell。
3. 將 PowerShell 目錄變更為已從下載的 ZIP 壓縮檔案解壓縮內容的資料夾。
4. 藉由執行下列命令，以執行名為 **AzureMigrateInstaller.ps1** 的指令碼：

    - 對於公用雲端： 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - 對於 Azure Government： 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    指令碼會在成功完成時啟動設備 Web 應用程式。

如果發生任何問題，您可以存取位於 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>時間戳記</em>.log 的指令碼記錄，以進行疑難排解。



### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備 VM 可以連線至[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure URL。

### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在任何可連線至設備的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址:44368**。

   或者，您也可以按一下應用程式捷徑，從桌面開啟應用程式。
2. 接受 **授權條款**，並閱讀第三方資訊。
1. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
    - **連線能力**：應用程式會確認伺服器是否能夠存取網際網路。 如果伺服器使用 Proxy：
        - 按一下 [設定 Proxy] 以指定 Proxy 位址 (格式為 http://ProxyIPAddress 或 http://ProxyFQDN) ) 和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
        - 如果您已新增 Proxy 詳細資料，或已停用 Proxy 和/或驗證，請按一下 [儲存] 以再次觸發連線檢查。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，伺服器探索才能正常運作。
    - **安裝更新**：Azure Migrate Server 評估會檢查設備是否已安裝最新的更新。檢查完成之後，您可以按一下 [檢視設備服務] 以查看設備上執行之元件的狀態和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 貼上從入口網站複製的 **Azure Migrate 專案金鑰**。 如果沒有金鑰，請移至 **伺服器評估 > 探索 > 管理現有的設備**，選取在金鑰產生時提供的設備名稱，並複製對應的金鑰。
1. 您將需要裝置程式碼來向 Azure 進行驗證。 按一下 **[登** 入] 將會開啟具有裝置程式碼的模式，如下所示。

    ![顯示裝置程式碼的強制回應](./media/tutorial-discover-vmware/device-code.png)

1. 按一下 **[複製程式碼] & 登** 入，以複製裝置程式碼，並在新的瀏覽器索引標籤中開啟 Azure 登入提示。如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
1. 在 [新增] 索引標籤上，貼上裝置程式碼，並使用您的 Azure 使用者名稱和密碼登入。
   
   不支援使用 PIN 登入。
3. 如果您不小心關閉 [登入] 索引標籤而未登入，則必須重新整理設備設定管理員的 [瀏覽器] 索引標籤，才能再次啟用 [登入] 按鈕。
1. 成功登入之後，請回到 [設備設定管理員] 的先前索引標籤。
4. 如果用於記錄的 Azure 使用者針對在金鑰產生期間建立的 Azure 資源帳戶具有正確的[權限](./tutorial-discover-physical.md)，就會起始設備註冊。
1. 成功註冊設備之後，您可以按一下 [檢視詳細資料]查看註冊詳細資料。


## <a name="start-continuous-discovery"></a>開始連續探索

現在，從設備連線至要探索的實體伺服器，然後開始探索。

1. 在 [步驟 1：選取復原點]**提供認證，以探索 Windows 和 Linux 實體或虛擬伺服器**，按一下 [新增認證]。
1. 若為 Windows server，請選取 **Windows Server** 作為來源類型、指定認證的自訂名稱並新增使用者名稱和密碼。按一下 [儲存]。
1. 如果您針對 Linux 伺服器使用密碼型驗證，請選取 **Linux Server (密碼型)** 作為來源類型、指定認證的自訂名稱並新增使用者名稱和密碼。按一下 [儲存]。
1. 如果您針對 Linux 伺服器使用 SSH 金鑰型驗證，可以選取 **Linux Server (密碼型)** 作為來源類型、指定認證的自訂名稱並新增使用者名稱、瀏覽並選取 SSH 私密金鑰檔案。 按一下 [ **儲存**]。

    - Azure Migrate 支援使用 RSA、DSA、ECDSA 和 ed25519 演算法的 ssh-keygen 命令所產生的 SSH 私密金鑰。
    - 目前 Azure Migrate 不支援複雜密碼型的 SSH 金鑰。 若沒有複雜密碼，請使用 SSH 金鑰。
    - 目前 Azure Migrate 不支援 PuTTY 所產生的 SSH 私密金鑰檔案。
    - Azure Migrate 支援 SSH 私密金鑰檔案的 OpenSSH 格式，如下所示：
    
    ![SSH 私密金鑰支援的格式](./media/tutorial-discover-physical/key-format.png)
1. 如果想要一次新增多個認證，請按一下 [新增更多] 以儲存並新增更多認證。 實體伺服器探索支援使用多個認證。
1. 在 **步驟 2：提供實體或虛擬伺服器詳細資料** 中，按一下 [新增探索來源] 來指定伺服器 **IP 位址/FQDN** 和認證的自訂名稱，以連線到伺服器。
1. 您可以一次 **新增單一項目**，或 **新增多個項目**。 另外也可透過 **匯入 CSV** 提供伺服器詳細資料。

    ![新增探索來源的選取項目](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - 如果您選擇 **新增單一項目**，可以選擇 OS 類型、指定認證的自訂名稱、新增伺服器 **IP 位址/FQDN**，然後按一下 [儲存]。
    - 如果您選擇 **新增多個項目**，可以在文字方塊中指定伺服器 **IP 位址/FQDN** 和認證的自訂名稱，一次新增多筆記錄。**確認** 新增的記錄，然後按一下 [儲存]。
    - 如果您選擇 **匯入 CSV** (預設選項)，您可以下載 CSV 範本檔案，並在檔案中填入伺服器 **IP 位址/FQDN** 和憑證的自訂名稱。 然後將檔案匯入設備，**確認** 檔案中的記錄，然後按一下 [儲存]。

1. 按一下儲存時，設備會嘗試驗證已新增的伺服器連線，並在資料表中顯示每部伺服器的 **驗證狀態**。
    - 如果伺服器驗證失敗，請按一下資料表狀態欄中的 [驗證失敗] 以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除伺服器，請選取 [刪除]。
1. 您可以在啟動探索之前，隨時 **重新驗證** 伺服器的連線功能是否正常。
1. 按一下 [開始探索]，以開始探索成功驗證的伺服器。 成功起始探索之後，您可以在資料表中檢查每部伺服器的探索狀態。


這會開始探索。 每部伺服器大約會需要 2 分鐘，才能讓探索到之伺服器的中繼資料出現在 Azure 入口網站中。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成後，您可以確認伺服器是否出現在入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器 >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器 計數。


## <a name="next-steps"></a>後續步驟

嘗試利用 Azure Migrate Server 評 [量來評估實體伺服器](tutorial-assess-physical.md) 。