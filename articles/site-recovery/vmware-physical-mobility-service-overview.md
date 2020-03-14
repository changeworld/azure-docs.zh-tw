---
title: 關於與 Azure Site Recovery 搭配使用來進行 VMware VM 和實體伺服器災害復原的行動服務 | Microsoft Docs
description: 了解與 Azure Site Recovery 服務搭配使用來進行將 VMware VM 和實體伺服器復原到 Azure 之災害復原的行動服務代理程式。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256831"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>關於適用於 VMware VM 和實體伺服器的行動服務

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 Site Recovery 行動服務。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。 您可以使用下列方法來部署「行動服務」︰

- [推入安裝](#push-installation)：當透過 Azure 入口網站啟用保護時，Site Recovery 會在伺服器上安裝行動代理程式。
- 手動安裝：您可以透過[UI](#install-mobility-agent-through-ui)或[命令提示](#install-mobility-agent-through-command-prompt)字元，在每部機器上手動安裝行動服務。
- [自動化部署](vmware-azure-mobility-install-configuration-mgr.md). 您可以使用 Configuration Manager 之類的軟體部署工具來自動化安裝。

> [!NOTE]
> 行動代理程式在 VMware Vm 或實體機器的來源機器上使用大約 6%-10% 的記憶體。

## <a name="anti-virus-on-replicated-machines"></a>在複寫的機器上防毒

如果您要複寫的機器正在執行作用中的防毒軟體，請務必從防毒作業中排除行動性服務安裝資料夾 (C:\ProgramData\ASR\agent)。 這可確保複寫運作正常。

## <a name="push-installation"></a>推送安裝

推入安裝是在入口網站中觸發的「[啟用](vmware-azure-enable-replication.md#enable-replication)複寫」作業不可或缺的一部分。 選擇您想要保護的虛擬機器集合並觸發「啟用複寫」之後，設定伺服器會將行動代理程式推入伺服器，安裝代理程式，並完成代理程式與設定伺服器的註冊。 若要順利完成此操作，

- 確保符合所有推入安裝的[必要條件](vmware-azure-install-mobility-service.md)。
- 確定伺服器的所有設定都落在[VMware 至 AZURE DR 案例的支援矩陣](vmware-physical-azure-support-matrix.md)之下。

下列各節已說明推播安裝工作流程的詳細資料。

### <a name="from-923-version-onwards"></a>從[9.23 版](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)起

在行動代理程式的推入安裝期間，會執行下列步驟

1. 將代理程式推送至來源機器。 將代理程式複製到來源電腦可能會因為多個環境錯誤而失敗。 請造訪[我們的指引](vmware-azure-troubleshoot-push-install.md)，針對推送安裝失敗進行疑難排解。
2. 當代理程式成功複製到伺服器上時，就會在伺服器上執行先決條件檢查。 如果不符合一或多個[必要條件](vmware-physical-azure-support-matrix.md)，安裝就會失敗。 如果符合所有必要條件，則會觸發安裝。
3. 在行動代理程式安裝過程中，會將 Azure Site Recovery VSS 提供者安裝在伺服器上。 此提供者是用來產生應用程式一致點。 如果 VSS 提供者的安裝失敗，將會略過此步驟，並繼續進行代理程式安裝。
4. 如果代理程式安裝成功，但 VSS 提供者安裝失敗，則作業狀態會標示為「警告」。 這不會影響損毀一致性點的產生。

    a. 若要產生應用程式一致點，請參閱[我們的指導](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)方針，以手動方式完成 Site Recovery VSS 提供者的安裝。 </br>
    b.  如果您不想要產生應用程式一致的點，請[修改複寫原則](vmware-azure-set-up-replication.md#create-a-policy)以關閉應用程式一致點。

### <a name="before-922-versions"></a>9\.22 之前的版本

1. 將代理程式推送至來源機器。 將代理程式複製到來源電腦可能會因為多個環境錯誤而失敗。 請造訪[我們的指引](vmware-azure-troubleshoot-push-install.md)，針對推送安裝失敗進行疑難排解。
2. 當代理程式成功複製到伺服器上時，就會在伺服器上執行先決條件檢查。 如果不符合一或多個[必要條件](vmware-physical-azure-support-matrix.md)，安裝就會失敗。 如果符合所有必要條件，則會觸發安裝。
3. 在行動代理程式安裝過程中，會將 Azure Site Recovery VSS 提供者安裝在伺服器上。 此提供者是用來產生應用程式一致點。 如果 VSS 提供者的安裝失敗，代理程式安裝將會失敗。 若要避免行動代理程式安裝失敗，請使用[9.23 版](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)或更新版本來產生損毀一致點，並手動安裝 VSS 提供者。

## <a name="install-mobility-agent-through-ui"></a>透過 UI 安裝行動代理程式

### <a name="prerequisite"></a>必要條件

- 確定伺服器的所有設定都落在[VMware 至 AZURE DR 案例的支援矩陣](vmware-physical-azure-support-matrix.md)之下。
- 根據伺服器的作業系統來[尋找安裝程式](#locate-installer-files)。

>[!IMPORTANT]
> 如果您要將 Azure IaaS VM 從一個 Azure 區域複寫到另一個，請不要使用此方法。 請改用以命令列為基礎的安裝方法。

1. 將安裝檔案複製到機器並執行。
2. 在 [安裝選項] 中，選取 [安裝行動服務]。
3. 選取安裝位置 > [安裝]。

    ![[行動服務安裝選項] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. 在 [安裝進度] 中監視安裝。 安裝完成後，選取 [繼續進行設定] ，以向設定伺服器註冊服務。

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. 在 [設定**伺服器詳細資料**] 中，指定您所設定的 IP 位址和複雜密碼。

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. 選取 [註冊] 完成註冊。

    ![[行動服務註冊完成] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>透過命令提示字元安裝行動代理程式

### <a name="prerequisite"></a>必要條件

- 確定伺服器的所有設定都落在[VMware 至 AZURE DR 案例的支援矩陣](vmware-physical-azure-support-matrix.md)之下。
- 根據伺服器的作業系統來[尋找安裝程式](#locate-installer-files)。

### <a name="on-a-windows-machine"></a>在 Windows 機器上

- 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 C:\Temp)。

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- 依以下方式進行安裝：

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- 向設定伺服器註冊代理程式。

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>安裝設定
**設定** | **詳細資料**
--- | ---
使用量 | Unifiedagent.exe/Role \<MS/MT >/InstallLocation \<安裝位置 >/Platform "VmWare"/Silent
安裝記錄 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 底下。
/Role | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
/InstallLocation| 選擇性參數。 指定行動服務安裝位置 (任何資料夾)。
/Platform | Mandatory。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。<br/><br/> 如果您要將 Azure Vm 視為實體機器，請指定**VMware**。
/Silent| 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定
**設定** | **詳細資料**
--- | ---
使用量 | Unifiedagentconfigurator.exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
代理程式設定記錄 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 底下。
/CSEndPoint | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
/PassphraseFilePath |  Mandatory。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="on-a-linux-machine"></a>在 Linux 機器上

1. 將安裝程式複製到您想要保護之伺服器上的本機資料夾 (例如 /tmp)。 在終端機中執行下列命令：

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. 依以下方式進行安裝：

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. 安裝完成後，行動服務必須向組態伺服器註冊。 執行下列命令來向組態伺服器註冊行動服務：

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>安裝設定
**設定** | **詳細資料**
--- | ---
使用量 | ./install-d \<安裝位置 >-r \<MS/MT >-v VmWare-q
-r | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
-d | 選擇性參數。 指定行動服務安裝位置：/usr/local/ASR。
-v | Mandatory。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。
-Q | 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定
**設定** | **詳細資料**
--- | ---
使用量 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  Mandatory。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

## <a name="azure-virtual-machine-agent"></a>Azure 虛擬機器代理程式

- **Windows VM**：從行動服務 9.7.0.0 版開始，會由行動服務安裝程式安裝 [Azure VM 代理程式](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 這可確保當機器容錯移轉至 Azure 時，Azure VM 會符合使用任何 VM 延伸模組的代理程式安裝先決條件。
- **Linux VM**：在容錯移轉之後，必須在 Azure VM 上手動安裝 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="locate-installer-files"></a>找出安裝程式檔案

移至設定伺服器上的%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 資料夾。 根據作業系統檢查您需要的安裝程式。 下表摘要說明每個 VMware VM 和實體伺服器作業系統的安裝程式檔案。 您可以先檢閱[支援的作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)，再開始進行操作。

**安裝程式檔案** | **作業系統 (僅限 64 位元)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016；Windows Server 2012 R2；Windows Server 2012；Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 伺服器
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>後續步驟

[設定行動服務的推入安裝](vmware-azure-install-mobility-service.md)。
