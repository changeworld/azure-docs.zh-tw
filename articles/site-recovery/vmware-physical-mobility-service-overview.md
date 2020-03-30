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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256831"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>關於適用於 VMware VM 和實體伺服器的行動服務

當您使用 [Azure Site Recovery](site-recovery-overview.md) 為 VMware VM 和實體伺服器設定災害復原時，會在每個內部部署 VMware VM 和實體伺服器上安裝 Site Recovery 行動服務。  行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。 您可以使用下列方法來部署「行動服務」︰

- [推送安裝](#push-installation)：當通過 Azure 門戶啟用保護時，網站恢復在伺服器上安裝移動代理。
- 手動安裝：您可以通過[UI](#install-mobility-agent-through-ui)或[命令提示符](#install-mobility-agent-through-command-prompt)在每台電腦上手動安裝移動服務。
- [自動部署](vmware-azure-mobility-install-configuration-mgr.md)：您可以使用組態管理員 等軟體部署工具自動安裝。

> [!NOTE]
> 移動代理在源電腦上使用大約 6%-10% 的記憶體用於 VMware VM 或物理電腦。

## <a name="anti-virus-on-replicated-machines"></a>在複寫的機器上防毒

如果您要複寫的機器正在執行作用中的防毒軟體，請務必從防毒作業中排除行動性服務安裝資料夾 (C:\ProgramData\ASR\agent**)。 這可確保複寫運作正常。

## <a name="push-installation"></a>推送安裝

推送安裝是門戶中觸發的"[啟用複製](vmware-azure-enable-replication.md#enable-replication)"作業的組成部分。 選擇要保護和觸發"啟用複製"的虛擬機器集後，佈建服務器會將移動代理推送到伺服器，安裝代理，並在佈建服務器完成代理註冊。 為了成功完成此操作，

- 確保滿足所有推送安裝[先決條件](vmware-azure-install-mobility-service.md)。
- 確保伺服器的所有配置都位於[VMware 到 Azure DR 方案的支援矩陣](vmware-physical-azure-support-matrix.md)中。

推送安裝工作流的詳細資訊已在以下部分仲介紹。

### <a name="from-923-version-onwards"></a>從[9.23 版本](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)開始

在移動代理的推送安裝過程中，執行以下步驟

1. 將代理推送到源電腦。 由於多個環境錯誤，將代理複製到源電腦可能會失敗。 請訪問[我們的指南](vmware-azure-troubleshoot-push-install.md)，解決推送安裝故障。
2. 在成功將代理複製到伺服器上後，在伺服器上執行先決條件檢查。 如果未滿足一個或多個[先決條件](vmware-physical-azure-support-matrix.md)，安裝將失敗。 如果滿足所有先決條件，將觸發安裝。
3. Azure 網站恢復 VSS 提供程式作為移動代理安裝的一部分安裝在伺服器上。 此提供程式用於生成應用程式一致點。 如果 VSS 提供程式的安裝失敗，將跳過此步驟，並且代理安裝將繼續。
4. 如果代理安裝成功，但 VSS 提供程式安裝失敗，則作業狀態將標記為"警告"。 這不會影響崩潰一致性點生成。

    a. 要生成應用程式一致點，請參閱[我們的指南](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)，以手動完成網站恢復 VSS 提供程式的安裝。 </br>
    b.  如果不希望生成應用程式一致點，[請修改複寫原則](vmware-azure-set-up-replication.md#create-a-policy)以關閉應用程式一致點。

### <a name="before-922-versions"></a>在 9.22 版本之前

1. 將代理推送到源電腦。 由於多個環境錯誤，將代理複製到源電腦可能會失敗。 請訪問[我們的指南](vmware-azure-troubleshoot-push-install.md)，解決推送安裝故障。
2. 在成功將代理複製到伺服器上後，在伺服器上執行先決條件檢查。 如果未滿足一個或多個[先決條件](vmware-physical-azure-support-matrix.md)，安裝將失敗。 如果滿足所有先決條件，將觸發安裝。
3. Azure 網站恢復 VSS 提供程式作為移動代理安裝的一部分安裝在伺服器上。 此提供程式用於生成應用程式一致點。 如果 VSS 提供程式的安裝失敗，則代理安裝將失敗。 為避免移動代理安裝失敗，請使用[9.23 版本](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)或更高版本生成崩潰一致性點並手動安裝 VSS 提供程式。

## <a name="install-mobility-agent-through-ui"></a>通過 UI 安裝移動代理

### <a name="prerequisite"></a>必要條件

- 確保伺服器的所有配置都位於[VMware 到 Azure DR 方案的支援矩陣](vmware-physical-azure-support-matrix.md)中。
- 根據伺服器的作業系統[查找安裝程式](#locate-installer-files)。

>[!IMPORTANT]
> 如果要將 Azure IaaS VM 從一個 Azure 區域複製到另一個 Azure 區域，請不要使用此方法。 改用基於命令列的安裝方法。

1. 將安裝檔案複製到機器並執行。
2. 在 [安裝選項]**** 中，選取 [安裝行動服務]****。
3. 選取安裝位置 > [安裝]****。

    ![[行動服務安裝選項] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. 在 [安裝進度]**** 中監視安裝。 安裝完成後，選取 [繼續進行設定]**** ，以向設定伺服器註冊服務。

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. 在**佈建服務器詳細資訊**中，指定您配置的 IP 位址和密碼。

    ![[行動服務註冊] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. 選取 [註冊]**** 完成註冊。

    ![[行動服務註冊完成] 頁面](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>通過命令提示安裝移動代理

### <a name="prerequisite"></a>必要條件

- 確保伺服器的所有配置都位於[VMware 到 Azure DR 方案的支援矩陣](vmware-physical-azure-support-matrix.md)中。
- 根據伺服器的作業系統[查找安裝程式](#locate-installer-files)。

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
使用量 | 統一代理.exe \</Role MS/MT \<> /安裝位置安裝位置> /平臺"VmWare"/靜音
安裝記錄 | 在 %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log 底下。
/Role | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
/InstallLocation| 選擇性參數。 指定行動服務安裝位置 (任何資料夾)。
/Platform | Mandatory。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。<br/><br/> 如果要將 Azure VM 視為物理電腦，請指定**VMware**。
/Silent| 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定
**設定** | **詳細資料**
--- | ---
使用量 | 統一代理配置器.exe /CSEndPoint \<CSIP> /\<密碼檔路徑密碼檔路徑>
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
使用量 | ./安裝 -d\<安裝位置> \<-r MS/MT> -v VmWare -q
-r | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
-d | 選擇性參數。 指定行動服務安裝位置：/usr/local/ASR。
-v | Mandatory。 指定要安裝行動服務的平台。 如果是 VMware VM/實體伺服器，則為 **VMware**；如果是 Azure VM，則為 **Azure**。
-Q | 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定
**設定** | **詳細資料**
--- | ---
使用量 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P 密碼檔路徑>
-i | 必要參數。 指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
-P |  Mandatory。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

## <a name="azure-virtual-machine-agent"></a>Azure 虛擬機器代理程式

- **Windows VM**：從行動服務 9.7.0.0 版開始，會由行動服務安裝程式安裝 [Azure VM 代理程式](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 這可確保當機器容錯移轉至 Azure 時，Azure VM 會符合使用任何 VM 延伸模組的代理程式安裝先決條件。
- **Linux VM**：在容錯移轉之後，必須在 Azure VM 上手動安裝 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="locate-installer-files"></a>查找安裝程式檔

轉到佈建服務器上的 %ProgramData%\ASR_home_svsystems_pushinstallsvc_存儲庫資料夾。 根據作業系統檢查您需要的安裝程式。 下表總結了每個 VMware VM 和物理伺服器作業系統的安裝程式檔。 您可以先檢閱[支援的作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)，再開始進行操作。

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
