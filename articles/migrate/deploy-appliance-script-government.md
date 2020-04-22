---
title: 在 Azure 政府中設定 Azure 移轉裝置
description: 瞭解如何在 Azure 政府中設定 Azure 遷移設備
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726731"
---
# <a name="set-up-an-appliance-in-azure-government"></a>在 Azure 政府中設定裝置 

請依照本文在 Azure 政府雲中部署用於 VMware VM、超 VM 和實體伺服器的[Azure 移轉裝置](deploy-appliance.md)。 執行文稿以建立設備,並驗證是否可以連接到 Azure。 如果要在公共雲中設置設備,請按照[本文](deploy-appliance-script.md)操作。


> [!NOTE]
> Azure 政府不支援使用範本部署設備的選項(對於 VMware VM 和超 VM)。


## <a name="prerequisites"></a>Prerequisites

該文本在現有物理電腦或 VM 上設置 Azure 遷移設備。

- 充當設備的計算機必須運行 Windows Server 2016,記憶體為 32 GB,具有 8 個 vCPU、大約 80 GB 的磁碟存儲和外部虛擬交換機。 它需要靜態或動態 IP 位址,並訪問互聯網。
- 在部署裝置之前,請查看[VMware VM、](migrate-appliance.md#appliance---vmware)[超 VM](migrate-appliance.md#appliance---hyper-v)和[實體伺服器](migrate-appliance.md#appliance---physical)的詳細設備要求。
- 不要在現有的 Azure 遷移設備上運行腳本。

## <a name="set-up-the-appliance-for-vmware"></a>為 VMware 設定產品

要為 VMware 設置設備,請從 Azure 門戶下載壓縮檔案,並提取內容。 運行 PowerShell 文稿以啟動設備 Web 應用。 設置設備並首次對其進行配置。 然後,將設備註冊到 Azure 遷移專案。

### <a name="download-the-script"></a>下載文稿

1.  在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2.  在 [探索機器]   > [機器是否已虛擬化?]  中，選取 [是，使用 VMWare vSphere Hypervisor]  。
3.  按下 **「下載**」,下載壓縮檔。 


### <a name="verify-file-security"></a>驗證檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. 驗證生成的哈希值。 對於最新版本:

    **演算法** | **雜湊值**
    --- | ---
    MD5 | 6316bcc8bc932204295bfe33f4be3949
          

### <a name="run-the-script"></a>執行指令碼

下面是腳稿的作用:

- 安裝代理和 Web 應用程式。
- 安裝 Windows 角色,包括 Windows 啟動服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可重寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新註冊表項 (HKLM),具有 Azure 遷移的持久設置。
- 建立紀錄和設定檔,如下所示:
    - **組態檔**: %ProgramData%\Microsoft Azure\Config
    - **記錄檔**: %ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔案提取到將承載設備的電腦上的資料夾。 確保沒有在現有 Azure 遷移設備上的電腦上運行文稿。
2. 使用管理員(提升)許可權在計算機上啟動 PowerShell。
3. 將 PowerShell 目錄更改為包含從下載的壓縮檔案中提取的內容的資料夾。
4. 執行文稿**AzureMigrate 安裝程式.ps1,** 如下所示:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. 文稿成功執行後,它會啟動設備 Web 應用程式,以便您可以設置設備。 如果遇到任何問題,請查看 C:_程序數據\微軟 Azure_Logs_AzureMigrateScenarioInstaller_<em>時間戳</em>.log 上的腳本日誌。

### <a name="verify-access"></a>驗證存取權限

確保設備可以連接到[政府雲](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-hyper-v"></a>為 Hyper-V 設定產品

要為 Hyper-V 設定設備,請從 Azure 門戶下載壓縮檔案,並提取內容。 運行 PowerShell 文稿以啟動設備 Web 應用。 設置設備並首次對其進行配置。 然後,將設備註冊到 Azure 遷移專案。

### <a name="download-the-script"></a>下載文稿

1.  在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2.  在 [探索機器]   > [機器是否已虛擬化?]  中，選取 [是，使用 Hyper-V]  。
3.  按下 **「下載**」,下載壓縮檔。 


### <a name="verify-file-security"></a>驗證檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. 驗證生成的哈希值。 對於最新版本:

    **演算法** | **雜湊值**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>執行指令碼

下面是腳稿的作用:

- 安裝代理和 Web 應用程式。
- 安裝 Windows 角色,包括 Windows 啟動服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可重寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新註冊表項 (HKLM),具有 Azure 遷移的持久設置。
- 建立紀錄和設定檔,如下所示:
    - **組態檔**: %ProgramData%\Microsoft Azure\Config
    - **記錄檔**: %ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔案提取到將承載設備的電腦上的資料夾。 確保沒有在現有 Azure 遷移設備上的電腦上運行文稿。
2. 使用管理員(提升)許可權在計算機上啟動 PowerShell。
3. 將 PowerShell 目錄更改為包含從下載的壓縮檔案中提取的內容的資料夾。
4. 執行文稿**AzureMigrate 安裝程式.ps1,** 如下所示:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. 文稿成功執行後,它會啟動設備 Web 應用程式,以便您可以設置設備。 如果遇到任何問題,請查看 C:_程序數據\微軟 Azure_Logs_AzureMigrateScenarioInstaller_<em>時間戳</em>.log 上的腳本日誌。

### <a name="verify-access"></a>驗證存取權限

確保設備可以連接到[政府雲](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="set-up-the-appliance-for-physical-servers"></a>設定為物理伺服器設定裝置

要為 VMware 設置設備,請從 Azure 門戶下載壓縮檔案,並提取內容。 運行 PowerShell 文稿以啟動設備 Web 應用。 設置設備並首次對其進行配置。 然後,將設備註冊到 Azure 遷移專案。

### <a name="download-the-script"></a>下載文稿

1.  在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2.  在 **「發現電腦** > **」中,您的電腦是否虛擬化?,** 選擇 **「未虛擬化/其他**」。。
3.  按下 **「下載**」,下載壓縮檔。 


### <a name="verify-file-security"></a>驗證檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. 驗證生成的哈希值。 對於最新版本:

    **演算法** | **雜湊值**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>執行指令碼

下面是腳稿的作用:

- 安裝代理和 Web 應用程式。
- 安裝 Windows 角色,包括 Windows 啟動服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可重寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新註冊表項 (HKLM),具有 Azure 遷移的持久設置。
- 建立紀錄和設定檔,如下所示:
    - **組態檔**: %ProgramData%\Microsoft Azure\Config
    - **記錄檔**: %ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔案提取到將承載設備的電腦上的資料夾。 確保沒有在現有 Azure 遷移設備上的電腦上運行文稿。
2. 使用管理員(提升)許可權在計算機上啟動 PowerShell。
3. 將 PowerShell 目錄更改為包含從下載的壓縮檔案中提取的內容的資料夾。
4. 執行文稿**AzureMigrate 安裝程式.ps1,** 如下所示:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. 文稿成功執行後,它會啟動設備 Web 應用程式,以便您可以設置設備。 如果遇到任何問題,請查看 C:_程序數據\微軟 Azure_Logs_AzureMigrateScenarioInstaller_<em>時間戳</em>.log 上的腳本日誌。

### <a name="verify-access"></a>驗證存取權限

確保設備可以連接到[政府雲](migrate-appliance.md#government-cloud-urls)的 Azure URL。

## <a name="next-steps"></a>後續步驟

部署設備後,需要首次對其進行配置,並將其註冊到 Azure 遷移專案。

- 為[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)設置產品。
- 為[Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)設置產品。
- 為[物理伺服器](how-to-set-up-appliance-physical.md)設置設備。