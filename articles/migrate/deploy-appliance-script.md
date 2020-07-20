---
title: 使用腳本設定 Azure Migrate 設備
description: 瞭解如何使用腳本設定 Azure Migrate 設備
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 47b6b35e62d484b4d7a33f6a53796c59e01817fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102441"
---
# <a name="set-up-an-appliance-with-a-script"></a>使用腳本設定設備

請遵循這篇文章來建立[Azure Migrate 應用裝置](./migrate-appliance-architecture.md)，以進行 VMware Vm 和 hyper-v vm 的評量/遷移。 您可以執行腳本來建立應用裝置，並確認它可以連線到 Azure。 

您可以使用腳本，或使用從 Azure 入口網站下載的範本，為 VMware 和 Hyper-v Vm 部署應用裝置。 如果您無法使用已下載的範本來建立 VM，則使用腳本會很有用。

- 若要使用範本，請遵循[VMware](tutorial-prepare-vmware.md)或[hyper-v](tutorial-prepare-hyper-v.md)的教學課程。
- 若要設定實體伺服器的設備，您只能使用腳本。 請遵循[這篇文章](how-to-set-up-appliance-physical.md)。
- 若要在 Azure Government 雲端中設定設備，請遵循[這篇文章](deploy-appliance-script-government.md)。

## <a name="prerequisites"></a>必要條件

腳本會在現有的實體機器或 VM 上設定 Azure Migrate 設備。

- 將作為設備的機器必須符合下列硬體和作業系統需求：

狀況 | 規格需求
--- | ---
VMware | Windows Server 2016，具有 32 GB 記憶體，八個 vcpu，大約 80 GB 的磁片儲存體
Hyper-V | Windows Server 2016，具有 16 GB 的記憶體，八個 vcpu，大約 80 GB 的磁片儲存體
- 電腦也需要外部虛擬交換器。 它需要靜態或動態 IP 位址，並可存取網際網路。
- 在您部署設備之前，請先參閱[VMware vm](migrate-appliance.md#appliance---vmware)、 [hyper-v vm](migrate-appliance.md#appliance---hyper-v)的詳細設備需求。
- 請勿在現有的 Azure Migrate 設備上執行腳本。

## <a name="set-up-the-appliance-for-vmware"></a>設定適用于 VMware 的設備

若要設定 VMware 的設備，您可以從[這裡](https://go.microsoft.com/fwlink/?linkid=2105112)下載名為 AzureMigrateInstaller.zip 的 zip 壓縮檔案，並將內容解壓縮。 您會執行 PowerShell 腳本來啟動設備 web 應用程式。 您會設定設備，並在第一次進行配置。 然後，向 Azure Migrate 專案註冊設備。


### <a name="verify-file-security"></a>驗證檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. 確認適用于 Azure 公用雲端的最新設備版本和腳本：

    **演算法** | **下載** | **SHA256**
    --- | --- | ---
    VMware (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0



### <a name="run-the-script"></a>執行指令碼

腳本會執行下列動作：

- 安裝代理程式和 web 應用程式。
- 會安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定來更新登錄機碼（HKLM）。
- 建立記錄檔和設定檔案，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄**檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並使用系統管理員（更高許可權）的許可權。
3. 將 PowerShell 目錄變更為包含從下載的壓縮檔案解壓縮之內容的資料夾。
4. 執行腳本**AzureMigrateInstaller.ps1**，如下所示：

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. 腳本執行成功之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔 AzureMigrateScenarioInstaller_<em>Timestamp</em>.log。

### <a name="verify-access"></a>驗證存取

請確定設備可以連接到[公用](migrate-appliance.md#public-cloud-urls)雲端的 Azure url。

## <a name="set-up-the-appliance-for-hyper-v"></a>設定 Hyper-v 的應用裝置

若要設定 Hyper-v 的應用裝置，您可以從[這裡](https://go.microsoft.com/fwlink/?linkid=2105112)下載名為 AzureMigrateInstaller.zip 的 zip 壓縮檔案，並將內容解壓縮。 您會執行 PowerShell 腳本來啟動設備 web 應用程式。 您會設定設備，並在第一次進行配置。 然後，向 Azure Migrate 專案註冊設備。


### <a name="verify-file-security"></a>驗證檔案安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 ZIP 檔案的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 範例： ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. 確認適用于 Azure 公用雲端的最新設備版本和腳本：

    **案例** | **下載** | **SHA256**
    --- | --- | ---
    Hyper-V (63.1 MB) | [最新版本](https://go.microsoft.com/fwlink/?linkid=2105112) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

### <a name="run-the-script"></a>執行指令碼

腳本會執行下列動作：

- 安裝代理程式和 web 應用程式。
- 會安裝 Windows 角色，包括 Windows 啟用服務、IIS 和 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [深入了解](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續性設定來更新登錄機碼（HKLM）。
- 建立記錄檔和設定檔案，如下所示：
    - **設定檔**：%ProgramData%\Microsoft Azure\Config
    - **記錄**檔：%ProgramData%\Microsoft Azure\Logs

執行指令碼：

1. 將壓縮檔案解壓縮至將裝載設備的電腦上的資料夾。 請確定您未在現有 Azure Migrate 設備的電腦上執行指令碼。
2. 在電腦上啟動 PowerShell，並使用系統管理員（更高許可權）的許可權。
3. 將 PowerShell 目錄變更為包含從下載的壓縮檔案解壓縮之內容的資料夾。
4. 執行腳本**AzureMigrateInstaller.ps1**，如下所示：``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 腳本執行成功之後，它會啟動設備 web 應用程式，讓您可以設定設備。 如果您遇到任何問題，請參閱 C:\ProgramData\Microsoft Azure\Logs\ 的腳本記錄檔 AzureMigrateScenarioInstaller_<em>Timestamp</em>.log。

### <a name="verify-access"></a>驗證存取

請確定設備可以連接到[公用](migrate-appliance.md#public-cloud-urls)雲端的 Azure url。

## <a name="next-steps"></a>後續步驟

部署設備之後，您必須第一次設定它，並向 Azure Migrate 專案註冊。

- 設定適用于[VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)的設備。
- 設定[hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)的應用裝置。
