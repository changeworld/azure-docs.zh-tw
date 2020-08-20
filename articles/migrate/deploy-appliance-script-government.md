---
title: 在 Azure Government 中設定 Azure Migrate 設備
description: 瞭解如何在 Azure Government 中設定 Azure Migrate 設備
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 993c25d7b2c580df47b61c836b885ed6379e8640
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612260"
---
# <a name="set-up-an-appliance-in-azure-government"></a>在 Azure Government 中設定設備 

遵循這篇文章，為 Azure Government 雲端中的 VMware Vm、Hyper-v Vm 和實體伺服器部署 [Azure Migrate 設備](./migrate-appliance-architecture.md) 。 您可以執行腳本來建立設備，並確認它可以連線到 Azure。 如果您想要在公用雲端中設定設備，請遵循 [這篇文章](deploy-appliance-script.md)。


> [!NOTE]
> Azure Government 不支援使用適用于 VMware Vm 和 Hyper-v Vm 的範本 (部署設備的選項) 。


## <a name="prerequisites"></a>Prerequisites

腳本會在現有的實體機器或 VM 上設定 Azure Migrate 設備。

- 將作為設備的電腦必須執行 Windows Server 2016 （具有 32 GB 的記憶體）、八個 vcpu、大約 80 GB 的磁片儲存體，以及外部虛擬交換器。 它需要靜態或動態 IP 位址，以及網際網路的存取權。
- 在您部署設備之前，請先參閱 [VMware vm](migrate-appliance.md#appliance---vmware)、 [hyper-v vm](migrate-appliance.md#appliance---hyper-v)和 [實體伺服器](migrate-appliance.md#appliance---physical)的詳細設備需求。
- 請勿在現有 Azure Migrate 設備上執行腳本。

## <a name="set-up-the-appliance-for-vmware"></a>設定適用于 VMware 的設備

若要設定適用于 VMware 的設備，您可以從 Azure 入口網站下載 zip 壓縮檔案，並將內容解壓縮。 您可以執行 PowerShell 腳本來啟動設備 web 應用程式。 您可以設定設備，並在第一次進行設定。 然後，您會向 Azure Migrate 專案註冊設備。

### <a name="download-the-script"></a>下載腳本

1.  在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]。
2.  在 [探索機器] > [機器是否已虛擬化?] 中，選取 [是，使用 VMware vSphere Hypervisor]。
3.  按一下 [ **下載**] 以下載 zip 壓縮檔案。 


### <a name="verify-file-security"></a>確認檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 確認最新的設備版本和雜湊值：

    **演算法** | **下載** | **SHA256**
    --- | --- | ---
    VMware (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="run-the-script"></a>執行指令碼

腳本的作用如下：

- 安裝代理程式和 web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定，更新 (HKLM) 的登錄機碼。
- 建立記錄檔和設定檔，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄**檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將 zip 壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並以系統管理員 (更高的) 許可權。
3. 將 PowerShell 目錄變更為包含從下載的 zip 壓縮檔案解壓縮的內容資料夾。
4. 執行腳本 **AzureMigrateInstaller.ps1**，如下所示： ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. 腳本順利執行之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔，AzureMigrateScenarioInstaller_<em>Timestamp</em>. .log。

### <a name="verify-access"></a>驗證存取

請確定設備可以連線到適用于 [政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure url。


## <a name="set-up-the-appliance-for-hyper-v"></a>設定 Hyper-v 的設備

若要設定 Hyper-v 的設備，您可以從 Azure 入口網站下載 zip 壓縮檔案，並將內容解壓縮。 您可以執行 PowerShell 腳本來啟動設備 web 應用程式。 您可以設定設備，並在第一次進行設定。 然後，您會向 Azure Migrate 專案註冊設備。

### <a name="download-the-script"></a>下載腳本

1.  在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]。
2.  在 [探索機器]**** > [機器是否已虛擬化?]**** 中，選取 [是，使用 Hyper-V]****。
3.  按一下 [ **下載**] 以下載 zip 壓縮檔案。 


### <a name="verify-file-security"></a>確認檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 確認最新的設備版本和雜湊值：

    **案例** | **下載** | **SHA256**
    --- | --- | ---
    Hyper-V (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3

          

### <a name="run-the-script"></a>執行指令碼

腳本的作用如下：

- 安裝代理程式和 web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定，更新 (HKLM) 的登錄機碼。
- 建立記錄檔和設定檔，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄**檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將 zip 壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並以系統管理員 (更高的) 許可權。
3. 將 PowerShell 目錄變更為包含從下載的 zip 壓縮檔案解壓縮的內容資料夾。
4. 執行腳本 **AzureMigrateInstaller.ps1**，如下所示： ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. 腳本順利執行之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔，AzureMigrateScenarioInstaller_<em>Timestamp</em>. .log。

### <a name="verify-access"></a>驗證存取

請確定設備可以連線到適用于 [政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure url。


## <a name="set-up-the-appliance-for-physical-servers"></a>設定實體伺服器的設備

若要設定適用于 VMware 的設備，您可以從 Azure 入口網站下載 zip 壓縮檔案，並將內容解壓縮。 您可以執行 PowerShell 腳本來啟動設備 web 應用程式。 您可以設定設備，並在第一次進行設定。 然後，您會向 Azure Migrate 專案註冊設備。

### <a name="download-the-script"></a>下載腳本

1.  在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]。
2.  在 [**探索機器**  >  **是否已虛擬化？**] 中，選取 [**未虛擬化/其他**]。
3.  按一下 [ **下載**] 以下載 zip 壓縮檔案。 


### <a name="verify-file-security"></a>確認檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 確認最新的設備版本和雜湊值：

    **案例** | **下載*** | **雜湊值**
    --- | --- | ---
    實體 (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1
          

### <a name="run-the-script"></a>執行指令碼

腳本的作用如下：

- 安裝代理程式和 web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定，更新 (HKLM) 的登錄機碼。
- 建立記錄檔和設定檔，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄**檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將 zip 壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並以系統管理員 (更高的) 許可權。
3. 將 PowerShell 目錄變更為包含從下載的 zip 壓縮檔案解壓縮的內容資料夾。
4. 執行腳本 **AzureMigrateInstaller.ps1**，如下所示： ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. 腳本順利執行之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔，AzureMigrateScenarioInstaller_<em>Timestamp</em>. .log。

### <a name="verify-access"></a>驗證存取

請確定設備可以連線到適用于 [政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure url。

## <a name="next-steps"></a>後續步驟

部署設備之後，您需要第一次進行設定，並向 Azure Migrate 專案註冊。

- 設定適用于 [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)的設備。
- 設定 [hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)的設備。
- 設定 [實體伺服器](how-to-set-up-appliance-physical.md)的設備。
