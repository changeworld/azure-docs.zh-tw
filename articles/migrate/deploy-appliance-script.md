---
title: 使用腳本設置 Azure 遷移設備
description: 瞭解如何使用腳本設置 Azure 遷移設備
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337684"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用腳本設置設備

本文介紹如何使用 PowerShell 安裝程式腳本設置[Azure 遷移設備](deploy-appliance.md)。

該腳本提供：
- 使用 OVA 範本設置設備，用於 VMware VM 的評估和無代理遷移。
- 使用 VHD 範本設置設備，用於評估和遷移超 VM 的替代方法。
- 為了評估物理伺服器（或要作為物理伺服器遷移的 VM），腳本是設置設備的唯一方法。

## <a name="prerequisites"></a>Prerequisites

該腳本在現有物理電腦或 VM 上設置 Azure 遷移設備。

- 充當設備的電腦必須運行 Windows Server 2016，記憶體為 32 GB，具有 8 個 vCPU、大約 80 GB 的磁片存儲和外部虛擬交換器。 它需要靜態或動態 IP 位址，並訪問互聯網。
- 在部署設備之前，請查看[VMware VM、](migrate-appliance.md#appliance---vmware)[超 VM](migrate-appliance.md#appliance---hyper-v)和[物理伺服器](migrate-appliance.md#appliance---physical)的詳細設備要求。
- 不要在現有的 Azure 遷移設備上運行腳本。


## <a name="download-the-script"></a>下載腳本

1. 找到將充當 Azure 遷移設備的電腦/VM。
2. 在機器上，執行以下操作：

    - 要將設備與 VMware VM 或超 V VM 一起使用，[請下載](https://go.microsoft.com/fwlink/?linkid=2105112)包含安裝程式腳本和 MSIs 的壓縮資料夾。
    - 要將設備與物理伺服器一起使用，請從 Azure 遷移門戶下載腳本，如[本教程](tutorial-assess-physical.md#set-up-the-appliance)中所述。

## <a name="verify-file-security"></a>驗證檔安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 驗證生成的雜湊值是否與這些設置匹配（對於最新版本）：

    **演算法** | **雜湊值**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>執行指令碼

下面是腳本的作用：

- 安裝代理和 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟動服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可重寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新登錄機碼 （HKLM），具有 Azure 遷移的持久設置。
- 創建日誌和設定檔，如下所示：
    - **組態檔**: %ProgramData%\Microsoft Azure\Config
    - **記錄檔**: %ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔提取到將承載設備的電腦上的資料夾。
2. 使用管理員（提升）許可權在電腦上啟動 PowerShell。
3. 將 PowerShell 目錄更改為包含從下載的壓縮檔中提取的內容的資料夾。
4. 運行腳本**AzureMigrate 安裝程式.ps1**如下：

    - 對於 VMware： 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - 對於 Hyper-V：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - 對於物理伺服器：
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. 腳本成功運行後，它會啟動設備 Web 應用程式，以便您可以設置設備。 如果遇到任何問題，可以在 C：_程式資料\微軟 Azure_logs_AzureMigrateScenarioInstaller_<em>時間戳記</em>.log 上查看腳本日誌。

## <a name="next-steps"></a>後續步驟

使用腳本設置設備後，請按照以下說明操作：

- 為[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)設置產品。
- 為[Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)設置產品。
- 為[物理伺服器](how-to-set-up-appliance-physical.md)設置設備。