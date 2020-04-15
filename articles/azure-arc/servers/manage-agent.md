---
title: 管理伺服器 (預覽) 代理的 Azure 弧
description: 本文介紹了在 Azure Arc 生命週期中為伺服器連接的計算機代理執行的不同管理任務。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308963"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理並維護連線的電腦代理

在 Windows 或 Linux 的伺服器(預覽)連接電腦代理的 Azure Arc 初始部署後,如果代理在其生命週期中已達到停用階段,則可能需要重新配置代理、升級代理或將其從電腦中刪除。 您可以手動或自動的方式輕鬆地管理這些例行維護工作，後者可以降低操作錯誤和費用。

## <a name="upgrading-agent"></a>升級代理

Windows 和 Linux 的 Azure 連接電腦代理可以手動或自動升級到最新版本,具體取決於您的要求。 下表描述了為執行代理升級而支援的方法。

| 作業系統 | 升級方法 |
|------------------|----------------|
| Windows | 手動<br> Windows Update |
| Ubuntu | [容易](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [齊珀](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| 紅帽企業, 亞馬遜, CentOS Linux | [百 勝](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 代理程式

要將 Windows 電腦上的代理更新到最新版本,該代理可從 Microsoft Update 獲得,並且可以使用現有的軟體更新管理過程進行部署。 也可以從指令提示符、文稿或其他自動化解決方案手動執行它,也可以通過執行 從 UI 精靈手`AzureConnectedMachine.msi`動執行 。 

> [!NOTE]
> * 要升級代理,您必須具有*管理員*許可權。
> * 要手動升級,必須首先將安裝程式包下載並複製到目標伺服器上的資料夾或從共用網路資料夾中。 

如果您不熟悉 Windows 安裝程式套件的指令列選項,請查看[Msiexec 標準命令列選項](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)與[Msiexec 命令列選項](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用設定精靈進行升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行**Azure 連接電腦代理.msi**以啟動安裝程式精靈。

安裝程式向導發現是否存在以前的版本,然後它會自動執行代理的升級。 升級完成後,設置嚮導將自動關閉。

#### <a name="to-upgrade-from-the-command-line"></a>從命令列升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 要靜默升級代理並在資料夾中建立設定紀錄檔,`C:\Support\Logs`請執行以下命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理程式

要將 Linux 電腦上的代理更新到最新版本,它涉及兩個命令。 一個命令使用儲存庫中的最新可用包清單更新本地包索引,以及一個用於升級本地包的命令。 

> [!NOTE]
> 要升級代理,您必須具有*根*訪問許可權或使用 Sudo 具有提升許可權的帳戶。

#### <a name="upgrade-ubuntu"></a>升級烏本圖

1. 要使用儲存函式庫中所做的最新變更更新本地端索引,請執行以下命令:

    ```bash
    apt update
    ```

2. 要升級系統,請執行以下命令:

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html)指令的作業(如套件的安裝與刪除)會記錄在紀錄檔中`/var/log/dpkg.log`。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升級紅帽/CentOS/亞馬遜Linux

1. 要使用儲存函式庫中所做的最新變更更新本地端索引,請執行以下命令:

    ```bash
    yum check-update
    ```

2. 要升級系統,請執行以下命令:

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet)指令的操作(如套件的安裝與刪除)會記錄在紀錄檔中`/var/log/yum.log`。 

#### <a name="upgrade-suse-linux-enterprise"></a>升級 SUSE Linux 企業

1. 要使用儲存函式庫中所做的最新變更更新本地端索引,請執行以下命令:

    ```bash
    zypper refresh
    ```

2. 要升級系統,請執行以下命令:

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper)指令的操作 (如套件的安裝與刪除)會記錄在紀錄檔`/var/log/zypper.log`中 。 

## <a name="about-the-azcmagent-tool"></a>關於阿茲cmagent工具

Azcmagent 工具 (Azcmagent.exe) 用於在安裝過程中為伺服器(預覽)連接的電腦代理設定 Azure Arc,或在安裝後修改代理的初始配置。 Azcmagent.exe 提供指令列參數以自訂代理並查看其狀態:

* **連線**- 將電腦連線到 Azure 弧線

* **離線**- 斷開電腦與 Azure 弧的連線

* **重新連線**- 將斷線連接的電腦重新連線到 Azure 弧

* **顯示**- 查看代理狀態及其配置屬性(資源組名稱、訂閱 ID、版本等),這些屬性在解決代理問題時會有所説明。

* **-h 或 -- 說明**- 顯示可用的指令列參數

    例如,要檢視 **「重新連線**」參數的詳細說明,請`azcmagent reconnect -h`鍵入 。 

* **-v 或 - 詳細**- 開啟詳細紀錄記錄

您可以在互動登入時手動執行**連接**、**斷開連線**和**重新連接**,或者使用用於將多個代理或 Microsoft 識別平台[存取權杖](../../active-directory/develop/access-tokens.md)的同一服務主體自動執行。 如果未使用服務主體將計算機註冊到 Azure Arc 伺服器(預覽),請參閱以下[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)以創建服務主體。

### <a name="connect"></a>連線

這裡指定 Azure 資源管理員中代表在 Azure 中建立電腦的資源。 資源位於指定的訂閱和資源組中,有關計算機的數據存儲在`--location`設置指定的 Azure 區域中。 如果未指定,預設資源名稱是此電腦的主機名。

然後,在本地下載並存儲與機器的系統分配標識對應的證書。 完成此步驟後,Azure 連接的計算機元數據服務和來賓配置代理將開始與伺服器的 Azure Arc 同步(預覽)。

要使用服務主體進行連接,請運行以下命令:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

要使用存取權杖進行連線,請執行以下指令:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

要使用提升的登入認證(互動式)進行連接,請執行以下命令:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>中斷連接

此參數指定 Azure 資源管理員中表示在 Azure 中刪除電腦的資源。 它不會從電腦中刪除代理,這必須作為單獨的步驟完成。 斷開電腦後,如果要將其重新註冊到伺服器 Azure Arc(預覽),`azcmagent connect`請使用 ,以便在 Azure 中為其創建新資源。

要使用服務主體斷開連接,請運行以下命令:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

要使用存取權杖斷連線,請執行以下指令:

`azcmagent disconnect --access-token <accessToken>`

要斷開與提升的登入認證(互動式)的連線,請執行以下命令:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>重新連接

此參數將已註冊或連接的電腦與伺服器 Azure Arc 重新連接(預覽)。 如果機器已關閉至少 45 天,其證書過期,則可能需要這樣做。 此參數使用提供的身份驗證選項來檢索對應於表示此計算機的 Azure 資源管理器資源的新認證。

此命令需要比[Azure 連接的電腦載入](overview.md#required-permissions)角色更高的許可權。

要使用服務主體重新連接,請運行以下命令:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

要使用存取權杖重新連線,請執行以下指令:

`azcmagent reconnect --access-token <accessToken>`

要重新連接已提升的登入認證(互動式),請執行以下命令:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>移除代理程式

執行以下方法之一,從電腦卸載 Windows 或 Linux 連接的電腦代理。 刪除代理不會將電腦與 Arc 登出為伺服器(預覽),這是當您不再需要在 Azure 中管理電腦時執行的單獨過程。

### <a name="windows-agent"></a>Windows 代理程式

以下兩種方法都移除代理,但它們不會刪除電腦上的*C:*程式檔案_AzureConnectedMachineAgent 資料夾*。

#### <a name="uninstall-from-control-panel"></a>從控制台解除安裝

1. 若要從機器解除安裝 Windows 代理程式，請執行下列動作：

    a. 使用具有系統管理員權限的帳戶登入電腦。  
    b. 在 [控制台]**** 中，選取 [程式和功能]****。  
    c. 在 [程式和功能]**** 中，依序選取 [Azure Connected Machine 代理程式]****、[解除安裝]**** 和 [是]****。  

    >[!NOTE]
    > 您也可以連按兩下 **AzureConnectedMachineAgent.msi** 安裝程式套件來執行代理程式安裝精靈。

#### <a name="uninstall-from-the-command-line"></a>從命令列解除安裝

要從命令提示符手動卸載代理或使用自動方法(如腳本),可以使用以下範例。 首先,您需要從作業系統中檢索產品代碼,即作為應用程式包的主要標識符的 GUID。 卸載使用 msiexec.exe 命令`msiexec /x {Product Code}`列 - 執行。
    
1. 開啟登錄編輯程式。

2. 在登錄機碼 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 底下，尋找並複製產品代碼 GUID。

3. 然後,您可以使用以下範例使用 Msiexec 卸載代理:

   * 從命令列型態:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 您可以使用 PowerShell 執行相同的步驟:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理程式

> [!NOTE]
> 要卸載代理,您必須具有*根*訪問許可權或使用 Sudo 具有提升許可權的帳戶。

要卸載 Linux 代理,要使用的命令取決於 Linux 作業系統。

- 對於 Ubuntu,執行以下命令:

    ```bash
    sudo apt purge azcmagent
    ```

- 對於 RHEL、CentOS 和 Amazon Linux,執行以下命令:

    ```bash
    sudo yum remove azcmagent
    ```

- 對於 SLES,執行以下指令:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>取消註冊電腦

如果計劃停止使用 Azure 中的支援服務管理電腦,請執行以下步驟,將電腦與 Arc 登出以用於伺服器(預覽)。 您可以在從電腦中刪除已連接的電腦代理之前或之後執行這些步驟。

1. 移至 [Azure 入口網站](https://aka.ms/hybridmachineportal)來開啟適用於伺服器的 Azure Arc (預覽)。

2. 依序選取清單中的機器、省略號 (**...**) 和 [刪除]****。
