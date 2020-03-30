---
title: 管理伺服器（預覽）代理的 Azure 弧
description: 本文介紹了在 Azure Arc 生命週期中為伺服器連接的電腦代理執行的不同管理工作。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367287"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理和維護連接的電腦代理

在 Windows 或 Linux 的伺服器（預覽）連接電腦代理的 Azure Arc 初始部署後，如果代理在其生命週期中已達到停用階段，則可能需要重新配置代理、升級代理或將其從電腦中刪除。 您可以手動或自動的方式輕鬆地管理這些例行維護工作，後者可以降低操作錯誤和費用。

## <a name="upgrading-agent"></a>升級代理

Windows 和 Linux 的 Azure 連接電腦代理可以手動或自動升級到最新版本，具體取決於您的要求。 下表描述了為執行代理升級而支援的方法。

| 作業系統 | 升級方法 |
|------------------|----------------|
| Windows | 手動<br> Windows Update |
| Ubuntu | [容易](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [齊珀](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| 紅帽企業， 亞馬遜， CentOS Linux | [百 勝](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 代理程式

要將 Windows 電腦上的代理更新到最新版本，該代理可從 Microsoft Update 獲得，並且可以使用現有的軟體更新管理過程進行部署。 也可以從命令提示符、腳本或其他自動化解決方案手動運行它，也可以通過執行 從 UI 嚮導手動運行`AzureConnectedMachine.msi`。 

> [!NOTE]
> * 要升級代理，您必須具有*管理員*許可權。
> * 要手動升級，必須首先將安裝套裝程式下載並複製到目標伺服器上的資料夾或從共用網路資料夾中。 

如果您不熟悉 Windows 安裝程式包的命令列選項，請查看[Msiexec 標準命令列選項](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)和[Msiexec 命令列選項](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用設置嚮導進行升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行**Azure 連接電腦代理.msi**以啟動安裝程式嚮導。

安裝程式嚮導發現是否存在以前的版本，然後它會自動執行代理的升級。 升級完成後，設置嚮導將自動關閉。

#### <a name="to-upgrade-from-the-command-line"></a>從命令列升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 要靜默升級代理並在資料夾中創建設置日誌檔，`C:\Support\Logs`請運行以下命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理程式

要將 Linux 電腦上的代理更新到最新版本，它涉及兩個命令。 一個命令使用存儲庫中的最新可用包清單更新本地包索引，以及一個用於升級本地包的命令。 

#### <a name="upgrade-ubuntu"></a>升級烏本圖

1. 要使用存儲庫中所做的最新更改更新本地包索引，請運行以下命令：

    ```bash
    apt update
    ```

2. 要升級系統，請運行以下命令：

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html)命令的操作（如包的安裝和刪除）將記錄在日誌檔中`/var/log/dpkg.log`。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升級紅帽/CentOS/亞馬遜Linux

1. 要使用存儲庫中所做的最新更改更新本地包索引，請運行以下命令：

    ```bash
    yum check-update
    ```

2. 要升級系統，請運行以下命令：

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet)命令的操作（如包的安裝和刪除）將記錄在日誌檔中`/var/log/yum.log`。 

#### <a name="upgrade-suse-linux-enterprise"></a>升級 SUSE Linux 企業

1. 要使用存儲庫中所做的最新更改更新本地包索引，請運行以下命令：

    ```bash
    zypper refresh
    ```

2. 要升級系統，請運行以下命令：

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper)命令的操作（如包的安裝和刪除）將記錄在日誌檔中`/var/log/zypper.log`。 

## <a name="remove-the-agent"></a>移除代理程式

使用以下過程之一使用本節中描述的命令列或設置嚮導卸載 Windows 或 Linux 代理。 在卸載代理之前，首先通過完成以下步驟斷開電腦與 Azure Arc 的伺服器（預覽）的連接： 

1. 移至 [Azure 入口網站](https://aka.ms/hybridmachineportal)來開啟適用於伺服器的 Azure Arc (預覽)。

2. 依序選取清單中的機器、省略號 (**...**) 和 [刪除]****。

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="uninstall-from-control-panel"></a>從控制台解除安裝

1. 若要從機器解除安裝 Windows 代理程式，請執行下列動作：

    a. 使用具有系統管理員權限的帳戶登入電腦。  
    b. 在 [控制台]**** 中，選取 [程式和功能]****。  
    c. 在 [程式和功能]**** 中，依序選取 [Azure Connected Machine 代理程式]****、[解除安裝]**** 和 [是]****。  

    >[!NOTE]
    > 您也可以連按兩下 **AzureConnectedMachineAgent.msi** 安裝程式套件來執行代理程式安裝精靈。

#### <a name="uninstall-from-the-command-line"></a>從命令列解除安裝

要從命令提示符手動卸載代理或使用自動方法（如腳本），可以使用以下示例。 首先，您需要從作業系統中檢索產品代碼，即作為應用程式包的主要識別碼的 GUID。 卸載使用 msiexec.exe 命令列 -`msiexec /x {Product Code}`執行。
    
1. 開啟登錄編輯程式。

2. 在登錄機碼 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 底下，尋找並複製產品代碼 GUID。

3. 然後，您可以使用以下示例使用 Msiexec 卸載代理：

   * 從命令列類型：

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 您可以使用 PowerShell 執行相同的步驟：

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理程式

要卸載 Linux 代理，要使用的命令取決於 Linux 作業系統。

- 對於 Ubuntu，運行以下命令：

    ```bash
    sudo apt purge azcmagent
    ```

- 對於 RHEL、CentOS 和 Amazon Linux，運行以下命令：

    ```bash
    sudo yum remove azcmagent
    ```

- 對於 SLES，運行以下命令：

    ```bash
    sudo zypper remove azcmagent
    ```
