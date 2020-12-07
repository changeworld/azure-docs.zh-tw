---
title: 使用腳本設定 Azure Migrate 設備
description: 瞭解如何使用腳本設定 Azure Migrate 設備
ms.topic: how-to
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 04/16/2020
ms.openlocfilehash: 38db1e68adde21d0e566256e9393f6fa4fc58824
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753785"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用腳本設定設備

遵循這篇文章來建立 [Azure Migrate 設備](./migrate-appliance-architecture.md) ，以進行 VMware Vm 和 hyper-v vm 的評量/遷移。 您可以執行腳本來建立設備，並確認它可以連線到 Azure。 

您可以使用腳本，或使用您從 Azure 入口網站下載的範本，為 VMware 和 Hyper-v Vm 部署設備。 如果您無法使用下載的範本來建立 VM，則使用腳本會很有用。

- 若要使用範本，請遵循 [VMware](./tutorial-discover-vmware.md) 或 [hyper-v](./tutorial-discover-hyper-v.md)的教學課程。
- 若要設定實體伺服器的設備，您只能使用腳本。 請遵循 [這篇文章](how-to-set-up-appliance-physical.md)。
- 若要在 Azure Government 雲端中設定設備，請遵循 [這篇文章](deploy-appliance-script-government.md)。

## <a name="prerequisites"></a>先決條件

腳本會在現有的實體機器或 VM 上設定 Azure Migrate 設備。

- 將作為設備的電腦必須符合下列硬體和作業系統需求：

案例 | 規格需求
--- | ---
VMware | Windows Server 2016 （具有 32 GB 的記憶體）、八個 vcpu、大約 80 GB 的磁片儲存體
Hyper-V | Windows Server 2016 （含 16 GB 的記憶體）、八個 vcpu、大約 80 GB 的磁片儲存體
- 電腦也需要外部虛擬交換器。 它需要靜態或動態 IP 位址，以及網際網路的存取權。
- 在您部署設備之前，請先參閱 [VMware vm](migrate-appliance.md#appliance---vmware)、 [hyper-v vm](migrate-appliance.md#appliance---hyper-v)的詳細設備需求。
- 請勿在現有 Azure Migrate 設備上執行腳本。

## <a name="set-up-the-appliance-for-vmware"></a>設定適用于 VMware 的設備

若要設定適用于 VMware 的設備，您可以從入口網站或從 [這裡](https://go.microsoft.com/fwlink/?linkid=2140334)下載名為 AzureMigrateInstaller-Server-Public.zip 的 zip 壓縮檔案，並將內容解壓縮。 您可以執行 PowerShell 腳本來啟動設備 web 應用程式。 您可以設定設備，並在第一次進行設定。 然後，您會向 Azure Migrate 專案註冊設備。


### <a name="verify-file-security"></a>確認檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. 確認適用于 Azure 公用雲端的最新設備版本和腳本：

    **演算法** | **下載** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72



### <a name="run-the-script"></a>執行指令碼

腳本的作用如下：

- 安裝代理程式和 web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定，更新 (HKLM) 的登錄機碼。
- 建立記錄檔和設定檔，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄** 檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將 zip 壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並以系統管理員 (更高的) 許可權。
3. 將 PowerShell 目錄變更為包含從下載的 zip 壓縮檔案解壓縮的內容資料夾。
4. 執行腳本 **AzureMigrateInstaller.ps1**，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. 腳本順利執行之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔，AzureMigrateScenarioInstaller_<em>Timestamp</em>. .log。

### <a name="verify-access"></a>驗證存取權

請確定設備可以連線至 [公用](migrate-appliance.md#public-cloud-urls) 雲端的 Azure url。

## <a name="set-up-the-appliance-for-hyper-v"></a>設定 Hyper-v 的設備

若要設定 Hyper-v 的設備，您可以從入口網站或從 [這裡](https://go.microsoft.com/fwlink/?linkid=2105112)下載名為 AzureMigrateInstaller-Server-Public.zip 的 zip 壓縮檔案，並將內容解壓縮。 您可以執行 PowerShell 腳本來啟動設備 web 應用程式。 您可以設定設備，並在第一次進行設定。 然後，您會向 Azure Migrate 專案註冊設備。


### <a name="verify-file-security"></a>確認檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. 確認適用于 Azure 公用雲端的最新設備版本和腳本：

    **案例** | **下載** | **SHA256**
    --- | --- | ---
    Hyper-V (85.8 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>執行指令碼

腳本的作用如下：

- 安裝代理程式和 web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定，更新 (HKLM) 的登錄機碼。
- 建立記錄檔和設定檔，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄** 檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將 zip 壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並以系統管理員 (更高的) 許可權。
3. 將 PowerShell 目錄變更為包含從下載的 zip 壓縮檔案解壓縮的內容資料夾。
4. 執行腳本 **AzureMigrateInstaller.ps1**，如下所示： 

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 腳本順利執行之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔，AzureMigrateScenarioInstaller_<em>Timestamp</em>. .log。

### <a name="verify-access"></a>驗證存取權

請確定設備可以連線至 [公用](migrate-appliance.md#public-cloud-urls) 雲端的 Azure url。

## <a name="next-steps"></a>後續步驟

部署設備之後，您需要第一次進行設定，並向 Azure Migrate 專案註冊。

- 設定適用于 [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)的設備。
- 設定 [hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)的設備。