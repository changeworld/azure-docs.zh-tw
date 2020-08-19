---
title: 關於 VMware Vm 和實體伺服器的嚴重損壞修復行動服務的 Azure Site Recovery |Microsoft Docs
description: 瞭解如何使用 Azure Site Recovery 服務，將 VMware Vm 和實體伺服器的災難復原至 Azure 的行動服務代理程式。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 431f1da463e4bd9970bc92b0842393f2de882220
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604737"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>關於適用於 VMware VM 和實體伺服器的行動服務

當您使用 [Azure Site Recovery](site-recovery-overview.md)為 VMware 虛擬機器設定嚴重損壞修復 (VM) 和實體伺服器時，您會在每個內部部署 VMware VM 和實體伺服器上安裝 Site Recovery 行動服務。 行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。 行動服務是由您可以使用下列方法部署的行動服務代理程式軟體所安裝：

- [推入安裝](#push-installation)：透過 Azure 入口網站啟用保護時，Site Recovery 會在伺服器上安裝行動服務。
- 手動安裝：您可以透過 [使用者介面 (UI) ](#install-the-mobility-service-using-ui) 或 [命令提示](#install-the-mobility-service-using-command-prompt)字元，在每部電腦上手動安裝行動服務。
- [自動化部署](vmware-azure-mobility-install-configuration-mgr.md)：您可以使用軟體部署工具（例如 Configuration Manager）來自動化行動服務安裝。

> [!NOTE]
> 行動服務在 VMware Vm 或實體機器的來源機器上使用大約6% 的記憶體。

## <a name="antivirus-on-replicated-machines"></a>已複寫機器上的防毒軟體

如果您想要複寫的機器是執行防毒軟體，請從防毒軟體作業中排除行動服務的安裝資料夾 _C:\ProgramData\ASR\agent_ 。 這種排除可確保複寫會如預期般運作。

## <a name="push-installation"></a>推入安裝

推入安裝是從 Azure 入口網站執行以 [啟用](vmware-azure-enable-replication.md#enable-replication)複寫的工作不可或缺的一部分。 選擇您想要保護並啟用複寫的 Vm 集合之後，設定伺服器會將行動服務代理程式推送至伺服器，安裝代理程式，並完成代理程式與設定伺服器的註冊。

### <a name="prerequisites"></a>先決條件

- 確定已符合所有的推送安裝 [必要條件](vmware-azure-install-mobility-service.md) 。
- 請確定所有伺服器設定都符合支援矩陣中的準則， [以便將 VMware vm 和實體伺服器故障復原至 Azure](vmware-physical-azure-support-matrix.md)。
- 從[9.36 版](https://support.microsoft.com/help/4578241/)開始，SUSE LINUX ENTERPRISE SERVER 11 SP4，請確定設定伺服器和相應放大[進程伺服器上有](#download-latest-mobility-agent-installer-for-suse-11-sp3-server)最新的安裝程式可供使用

下列各節將說明推入安裝工作流程：

### <a name="mobility-service-agent-version-923-and-higher"></a>行動服務代理程式版本9.23 和更高版本

如需版本9.23 的詳細資訊，請參閱 [Azure Site Recovery 的更新彙總套件 35](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)。

在行動服務的推送安裝期間，執行下列步驟：

1. 代理程式會推送至來源電腦。 將代理程式複製到來源電腦可能會因為多個環境錯誤而失敗。 請造訪 [我們的指引](vmware-azure-troubleshoot-push-install.md) ，以針對推送安裝失敗進行疑難排解。
1. 將代理程式成功複製到伺服器之後，會在伺服器上執行先決條件檢查。
   - 如果符合所有必要條件，則會開始安裝。
   - 如果不符合一或多個 [必要條件](vmware-physical-azure-support-matrix.md) ，安裝就會失敗。
1. 在安裝代理程式的過程中，會安裝 Azure Site Recovery 的磁碟區陰影複製服務 (VSS) 提供者。 VSS 提供者是用來產生應用程式一致的復原點。 如果 VSS 提供者的安裝失敗，則會略過此步驟，並繼續進行代理程式安裝。
1. 如果代理程式安裝成功，但 VSS 提供者安裝失敗，則工作狀態會標示為 [ **警告**]。 這不會影響損毀一致的復原點產生。

    - 若要產生應用程式一致的復原點，請參閱 [我們的指導](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) 方針，以完成手動安裝 Site Recovery VSS 提供者。
    - 如果您不想要產生應用程式一致的復原點，請 [修改複寫原則](vmware-azure-set-up-replication.md#create-a-policy) 以關閉應用程式一致的復原點。

### <a name="mobility-service-agent-version-922-and-below"></a>行動服務代理程式9.22 版和以下版本

1. 代理程式會推送至來源電腦。 將代理程式複製到來源電腦可能會因為多個環境錯誤而失敗。 請參閱 [我們的指引](vmware-azure-troubleshoot-push-install.md) ，以針對推送安裝失敗進行疑難排解。
1. 將代理程式成功複製到伺服器之後，會在伺服器上執行先決條件檢查。
   - 如果符合所有必要條件，則會開始安裝。
   - 如果不符合一或多個 [必要條件](vmware-physical-azure-support-matrix.md) ，安裝就會失敗。

1. 在安裝代理程式的過程中，會安裝 Azure Site Recovery 的磁碟區陰影複製服務 (VSS) 提供者。 VSS 提供者是用來產生應用程式一致的復原點。
   - 如果 VSS 提供者安裝失敗，代理程式安裝將會失敗。 若要避免代理程式安裝失敗，請使用 [9.23 版](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 或更高版本來產生損毀一致復原點，並手動安裝 VSS 提供者。

## <a name="install-the-mobility-service-using-ui"></a>使用 UI 安裝行動服務

### <a name="prerequisites"></a>先決條件

- 請確定所有伺服器設定都符合支援矩陣中的準則， [以便將 VMware vm 和實體伺服器故障復原至 Azure](vmware-physical-azure-support-matrix.md)。
- 找出伺服器作業系統[的安裝程式](#locate-installer-files)。

>[!IMPORTANT]
> 如果您要將 Azure 基礎結構即服務 (IaaS) VM 從一個 Azure 區域複寫至另一個 Azure 區域，請勿使用 UI 安裝方法。 使用 [命令提示](#install-the-mobility-service-using-command-prompt) 字元安裝。

1. 將安裝檔案複製到機器並執行。
1. 在 [安裝選項]**** 中，選取 [安裝行動服務]****。
1. 選擇安裝位置，然後選取 [ **安裝**]。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="行動服務安裝選項] 頁面。":::

1. 在 [安裝進度]**** 中監視安裝。 安裝完成後，選取 [繼續進行設定]**** ，以向設定伺服器註冊服務。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="行動服務的註冊頁面。":::

1. 在 [設定 **伺服器詳細資料**] 中，指定您所設定的 IP 位址和複雜密碼。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="行動服務的註冊頁面。":::

1. 選取 [註冊]**** 完成註冊。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="行動服務註冊最終頁面。":::

## <a name="install-the-mobility-service-using-command-prompt"></a>使用命令提示字元安裝行動服務

### <a name="prerequisites"></a>先決條件

- 請確定所有伺服器設定都符合支援矩陣中的準則， [以便將 VMware vm 和實體伺服器故障復原至 Azure](vmware-physical-azure-support-matrix.md)。
- 找出伺服器作業系統[的安裝程式](#locate-installer-files)。

### <a name="windows-machine"></a>Windows 電腦

- 從命令提示字元執行下列命令，以將安裝程式複製到您想要保護之伺服器上的本機資料夾（例如 _C：\Temp_）。 以實際的檔案名取代安裝程式的檔案名。

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 執行此命令以安裝代理程式。

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 執行這些命令，以向設定伺服器註冊代理程式。

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>安裝設定

設定 | 詳細資料
--- | ---
語法 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
安裝程式記錄 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
`/InstallLocation`| 選擇性參數。 指定行動服務安裝位置 (任何資料夾)。
`/Platform` | Mandatory。 指定安裝行動服務的平臺： <br/> **Vmware 適用于 Vmware** vm/實體伺服器。 <br/> 適用于 Azure Vm 的**azure** 。<br/><br/> 如果您要將 Azure Vm 視為實體機器，請指定 **VMware**。
`/Silent`| 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

設定 | 詳細資料
--- | ---
語法 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
代理程式設定記錄 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 必要參數。 `<CSIP>` 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
`/PassphraseFilePath` |  Mandatory。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="linux-machine"></a>Linux 機器

1. 從終端機會話，將安裝程式複製到您要保護之伺服器上的本機資料夾（例如 _/tmp_ ）。 將安裝程式的檔案名取代為您的 Linux 發行版本的實際檔案名，然後執行命令。

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 依以下方式進行安裝：

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 安裝完成之後，行動服務必須註冊到設定伺服器。 執行下列命令，向設定伺服器註冊行動服務。

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>安裝設定

設定 | 詳細資料
--- | ---
語法 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
`-d` | 選擇性參數。 指定行動服務安裝位置： `/usr/local/ASR` 。
`-v` | Mandatory。 指定安裝行動服務的平臺。 <br/> **Vmware 適用于 Vmware** vm/實體伺服器。 <br/> 適用于 Azure Vm 的**azure** 。
`-q` | 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

設定 | 詳細資料
--- | ---
語法 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 必要參數。 `<CSIP>` 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
`-P` |  Mandatory。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

## <a name="azure-virtual-machine-agent"></a>Azure 虛擬機器代理程式

- **Windows VM**：從行動服務 9.7.0.0 版開始，會由行動服務安裝程式安裝 [Azure VM 代理程式](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 這可確保當機器容錯移轉至 Azure 時，Azure VM 會符合使用任何 VM 擴充功能的代理程式安裝先決條件。
- **Linux VM**：在容錯移轉之後，必須在 Azure VM 上手動安裝 [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md)。

## <a name="locate-installer-files"></a>尋找安裝程式檔案

在設定伺服器上，移至資料夾 _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_。 根據作業系統檢查您需要的安裝程式。 下表摘要說明每個 VMware VM 和實體伺服器作業系統的安裝程式檔案。 開始之前，您可以檢查支援的 [作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)。

> [!NOTE]
> 檔案名會使用下表所示的語法，並將 _版本_ 和 _日期_ 做為實際值的預留位置。 實際的檔案名看起來會像這些範例：
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

安裝程式檔案 | 作業系統 (僅限 64 位元)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> 包括 SP2 和 SP3。
[以手動方式下載並放置在此資料夾中](#download-latest-mobility-agent-installer-for-suse-11-sp3-server)。 | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6。4 </br> Oracle Enterprise Linux 6。5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 伺服器
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

### <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-server"></a>下載 SUSE 11 SP3 伺服器的最新行動代理程式安裝程式

從[9.36 版](https://support.microsoft.com/help/4578241/)開始，**更新或保護 SUSE Linux Enterprise Server 11 SP3 電腦**的必要條件如下：

1. 確定已從 Microsoft 下載中心下載最新的行動代理程式安裝程式，並放在設定伺服器和所有 scale out 進程伺服器上的推送安裝程式存放庫中
2. [下載](https://download.microsoft.com/download/0/3/4/0341b388-1ff5-4ead-b197-7cf6d2bb3e40/Microsoft-ASR_UA_9.36.0.0_SLES11-SP3-64_GA_06Aug2020_release.tar.gz) SUSE LINUX ENTERPRISE SERVER 11 SP3 代理程式安裝程式。
3. 流覽至設定伺服器，複製下列路徑上的 SUSE Linux Enterprise Server 11 SP3 代理程式安裝程式
    1. INSTALL_DIR \home\svsystems\pushinstallsvc\repository
    1.  INSTALL_DIR \home\svsystems\admin\web\sw 資料夾
4. 現在，流覽至相關聯的相應放大進程伺服器 & 在第三個步驟所述的兩個路徑中複製安裝程式。
5. **例如**，如果安裝路徑為 C:\Program Files (x86) \microsoft Azure Site Recovery，則上述目錄會是
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\admin\web\sw path

## <a name="next-steps"></a>後續步驟

[設定行動服務的推入安裝](vmware-azure-install-mobility-service.md)。
