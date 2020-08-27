---
title: 設定實體伺服器的 Azure Migrate 設備
description: 瞭解如何設定 Azure Migrate 設備以進行實體伺服器評量。
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 1b4e875a81c92f74cd7d2db96cf1c313157297eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923535"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>設定實體伺服器的設備

本文說明如果您要使用 Azure Migrate：伺服器評估工具來評估實體伺服器，如何設定 Azure Migrate 設備。

Azure Migrate 設備是輕量設備，可供 Azure Migrate 伺服器評估用來執行下列作業：

- 探索內部部署伺服器。
- 將探索到的伺服器的中繼資料和效能資料傳送到 Azure Migrate 伺服器評量。

[深入瞭解](migrate-appliance.md) Azure Migrate 設備。


## <a name="appliance-deployment-steps"></a>設備部署步驟

若要設定設備，請：
- 提供設備名稱，並在入口網站中產生 Azure Migrate 專案金鑰。
- 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
- 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
- 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
- 第一次設定設備，並使用 Azure Migrate 專案金鑰向 Azure Migrate 專案註冊。

### <a name="generate-the-azure-migrate-project-key"></a>產生 Azure Migrate 專案金鑰

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 [**探索機器**  >  **是否已虛擬化電腦？**] 中，選取 [**實體] 或 [其他] (AWS、GCP、Xen 等 ) **。
3. 在 **1：產生 Azure Migrate 專案金鑰**中，為您將設定用來探索實體或虛擬伺服器的 Azure Migrate 設備提供名稱。名稱應該是英數位元，且長度不得超過14個字元。
1. 按一下 [ **產生金鑰** ] 以開始建立必要的 Azure 資源。 在資源建立期間，請勿關閉 [探索電腦] 頁面。
1. 成功建立 Azure 資源之後，就會產生 **Azure Migrate 的專案金鑰** 。
1. 複製金鑰，因為您需要它才能在其設定期間完成設備註冊。

### <a name="download-the-installer-script"></a>下載安裝程式指令碼

在 **2：下載 Azure Migrate 設備**中，按一下 [ **下載**]。

   ![探索電腦的選項](./media/tutorial-assess-physical/servers-discover.png)


   ![產生金鑰的選取專案](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公用雲端的使用範例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 政府雲端的使用範例：```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  確認設備的最新版本，以及雜湊值 [設定](./tutorial-assess-physical.md#verify-security)。
 

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
2. 接受 **授權條款**，並閱讀協力廠商資訊。
1. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
    - **連線能力**：應用程式會確認伺服器是否能夠存取網際網路。 如果伺服器使用 Proxy：
        - 按一下 [ **設定 proxy** ]，並在表單 http://ProxyIPAddress 或 http://ProxyFQDN) 和接聽埠中指定 proxy 位址 (。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
        - 如果您已新增 proxy 詳細資料或停用 proxy 及/或驗證，請按一下 [ **儲存** ] 以觸發連線檢查。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，伺服器探索才能正常運作。
    - **安裝更新**： Azure Migrate Server 評定會檢查設備是否已安裝最新的更新。檢查完成後，您可以按一下 [ **View 設備服務** ] 以查看設備上執行的元件狀態和版本。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 貼上從入口網站複製的 **Azure Migrate 專案金鑰** 。 如果您沒有金鑰，請移至 [ **伺服器評量]> 探索> 管理現有的設備**]，選取您在產生金鑰時所提供的設備名稱，並複製對應的金鑰。
1. 按一下 [ **登入**]。 它會在新的瀏覽器索引標籤中開啟 Azure 登入提示。如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
1. 在新的索引標籤上，使用您的 Azure 使用者名稱和密碼登入。
   
   不支援使用 PIN 登入。
3. 成功登入後，返回 web 應用程式。 
4. 如果用於記錄的 Azure 使用者帳戶具有在金鑰產生期間所建立之 Azure 資源的正確 [許可權](tutorial-prepare-physical.md) ，則會起始設備註冊。
1. 成功註冊設備之後，您可以按一下 [ **查看詳細資料**] 來查看註冊詳細資料。


## <a name="start-continuous-discovery"></a>開始連續探索

現在，從設備連線至要探索的實體伺服器，然後開始探索。

1. 在 **步驟1：提供探索 windows 和 linux 實體或虛擬伺服器的認證**中，按一下 [ **新增認證** ] 來指定認證的易記名稱，並 **新增 Windows** 或 Linux 伺服器的使用者名稱和 **密碼** 。 按一下 [ **儲存**]。
1. 如果您想要一次新增多個認證，請按一下 [ **新增其他** ] 以儲存並新增更多認證。 實體伺服器探索支援多個認證。
1. 在 [ **步驟2：提供實體或虛擬伺服器詳細資料**] 中，按一下 [ **新增探索來源** ]，以指定伺服器 **IP 位址/FQDN** 和認證的易記名稱，以連接到伺服器。
1. 您可以一次 **新增一個專案** ，或在一個 Go 中 **加入多個專案** 。 另外還有一個可透過匯 **入 CSV**提供伺服器詳細資料的選項。

    ![新增探索來源的選項](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - 如果您選擇 [ **新增單一專案**]，可以選擇 [OS 類型]、[指定認證的易記名稱]、[新增伺服器 **IP 位址/FQDN** ]，然後按一下 [ **儲存**]。
    - 如果您選擇 [ **新增多個專案**]，可以一次新增多筆記錄，方法是在文字方塊中指定伺服器 **IP 位址/FQDN** 和認證的易記名稱。**確認** 新增的記錄，然後按一下 [ **儲存**]。
    - 如果您選擇 [匯 **入 csv** _ (預設為選取) _，您可以下載 csv 範本檔案，在檔案中填入伺服器的 **IP 位址/FQDN** 和易記的認證名稱。 然後，將檔案匯入設備中， **確認** 檔案中的記錄，然後按一下 [ **儲存**]。

1. 按一下 [儲存] 時，設備會嘗試驗證與所加入之伺服器的連接，並針對每部伺服器顯示資料表中的 **驗證狀態** 。
    - 如果伺服器驗證失敗，請在資料表的 [狀態] 資料行中按一下 [ **驗證失敗** ] 來檢查錯誤。 請修正問題，然後再次驗證。
    - 若要移除伺服器，請按一下 [ **刪除**]。
1. 在開始探索之前，您可以隨時重新 **驗證** 伺服器的連線能力。
1. 按一下 [ **開始探索] 開始**探索成功驗證的伺服器。 成功起始探索之後，您可以針對資料表中的每一部伺服器檢查探索狀態。


這會開始探索。 每一部伺服器大約需要2分鐘的時間，探索到的伺服器的中繼資料才會出現在 Azure 入口網站中。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成之後，您可以確認伺服器出現在入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器 >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器 計數。


## <a name="next-steps"></a>後續步驟

嘗試利用 Azure Migrate Server 評 [量來評估實體伺服器](tutorial-assess-physical.md) 。
