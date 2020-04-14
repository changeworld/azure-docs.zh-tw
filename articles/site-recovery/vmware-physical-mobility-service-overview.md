---
title: 關於使用 Azure 網站恢復的 VMware VM 和物理伺服器的災難恢復行動服務 |微軟文件
description: 使用 Azure 網站恢復服務瞭解用於將 VMware VM 和物理伺服器災難恢復的行動服務代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259781"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>關於適用於 VMware VM 和實體伺服器的行動服務

使用[Azure 網站恢復](site-recovery-overview.md)為 VMware 虛擬機器 (VM) 和實體伺服器設定災難復原時,將站點恢復行動服務安裝在每個本地 VMware VM 和物理伺服器上。 行動服務會擷取機器上的資料寫入，然後將它們轉送給 Site Recovery 處理伺服器。 移動服務由行動服務代理程式安裝,您可以使用以下方法進行部署:

- [推送安裝](#push-installation):當通過 Azure 門戶啟用保護時,網站恢復將安裝在伺服器上的行動服務。
- 手動安裝:您可以通過[使用者介面 (UI)](#install-the-mobility-service-using-ui)或[命令提示符](#install-the-mobility-service-using-command-prompt)在每台電腦上手動安裝行動服務。
- [自動部署](vmware-azure-mobility-install-configuration-mgr.md):您可以使用配置管理員 等軟體部署工具自動安裝行動服務。

> [!NOTE]
> 移動服務在源電腦上使用大約 6%-10% 的記憶體用於 VMware VM 或物理電腦。

## <a name="antivirus-on-replicated-machines"></a>複製的電腦上防病毒

如果要複製的電腦正在執行防病毒軟體,則從防病毒操作中排除行動服務的安裝資料夾_C:_ProgramData_ASR\代理_。 此排除可確保複製按預期工作。

## <a name="push-installation"></a>推送安裝

推送安裝是從 Azure 門戶運行以[啟用複製](vmware-azure-enable-replication.md#enable-replication)作業的一個組成部分。 選擇要保護和啟用複製的 VM 集後,設定伺服器會將行動服務代理推送到伺服器,安裝代理,並完成代理在配置伺服器的註冊。

### <a name="prerequisites"></a>Prerequisites

- 確保滿足所有推送安裝[先決條件](vmware-azure-install-mobility-service.md)。
- 確保所有伺服器配置都符合[支援矩陣中的標準,以便 VMware VM 和物理伺服器恢復到 Azure。](vmware-physical-azure-support-matrix.md)

推送安裝工作流在以下部分中介紹:

### <a name="mobility-service-agent-version-923-and-higher"></a>行動服務代理版本 9.23 及更高版本

有關版本9.23的詳細資訊,請參閱[Azure網站恢復更新匯總35。](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)

在行動服務推送安裝期間,執行以下步驟:

1. 代理被推送到源電腦。 由於多個環境錯誤,將代理複製到源電腦可能會失敗。 請造訪[我們的指南](vmware-azure-troubleshoot-push-install.md),解決推送安裝故障。
1. 成功將代理複製到伺服器后,對伺服器執行先決條件檢查。
   - 如果滿足所有先決條件,則開始安裝。
   - 如果未滿足一個或多個[先決條件](vmware-physical-azure-support-matrix.md),安裝將失敗。
1. 作為代理安裝的一部分,將安裝用於 Azure 網站恢復的卷卷影複製服務 (VSS) 提供程式。 VSS 提供程式用於生成應用程式一致的恢復點。 如果 VSS 提供程式的安裝失敗,將跳過此步驟,並且代理安裝將繼續。
1. 若代理安裝成功,但 VSS 提供程式安裝失敗,則作業狀態將標記為**警告**。 這不會影響崩潰一致的恢復點生成。

    - 要生成應用程式一致的恢復點,請參閱[我們的指南](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine),以完成網站恢復 VSS 提供程式的手動安裝。
    - 如果不想產生應用程式一致的復原點,[請修改複製策略](vmware-azure-set-up-replication.md#create-a-policy)以關閉應用程式一致的復原點。

### <a name="mobility-service-agent-version-922-and-below"></a>行動服務代理版本 9.22 及以下版本

1. 代理被推送到源電腦。 由於多個環境錯誤,將代理複製到源電腦可能會失敗。 請參閱[我們的指南](vmware-azure-troubleshoot-push-install.md),以排除推送安裝故障。
1. 成功將代理複製到伺服器后,對伺服器執行先決條件檢查。
   - 如果滿足所有先決條件,則開始安裝。
   - 如果未滿足一個或多個[先決條件](vmware-physical-azure-support-matrix.md),安裝將失敗。

1. 作為代理安裝的一部分,將安裝用於 Azure 網站恢復的卷卷影複製服務 (VSS) 提供程式。 VSS 提供程式用於生成應用程式一致的恢復點。
   - 如果 VSS 提供程式安裝失敗,代理安裝將失敗。 為避免代理安裝失敗,請使用版本[9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)或更高版本生成崩潰一致的復原點,並手動安裝 VSS 提供程式。

## <a name="install-the-mobility-service-using-ui"></a>使用 UI 安裝行動服務

### <a name="prerequisites"></a>Prerequisites

- 確保所有伺服器配置都符合[支援矩陣中的標準,以便 VMware VM 和物理伺服器恢復到 Azure。](vmware-physical-azure-support-matrix.md)
- [尋找](#locate-installer-files)伺服器作業系統的安裝程式。

>[!IMPORTANT]
> 如果要將 Azure 基礎結構作為服務 (IaaS) VM 從一個 Azure 區域複製到另一個 Azure 區域,則不要使用 UI 安裝方法。 使用[命令提示](#install-the-mobility-service-using-command-prompt)安裝。

1. 將安裝檔案複製到機器並執行。
1. 在 [安裝選項]**** 中，選取 [安裝行動服務]****。
1. 選擇安裝位置並選擇 **「安裝**」 。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="移動服務安裝選項頁。":::

1. 在 [安裝進度]**** 中監視安裝。 安裝完成後，選取 [繼續進行設定]**** ，以向設定伺服器註冊服務。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="移動服務註冊頁面。":::

1. 在**設定伺服器詳細資訊**中,指定您設定的 IP 位址和密碼。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="移動服務註冊頁面。":::

1. 選取 [註冊]**** 完成註冊。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="移動服務註冊最終頁面。":::

## <a name="install-the-mobility-service-using-command-prompt"></a>使用指令提示安裝行動服務

### <a name="prerequisites"></a>Prerequisites

- 確保所有伺服器配置都符合[支援矩陣中的標準,以便 VMware VM 和物理伺服器恢復到 Azure。](vmware-physical-azure-support-matrix.md)
- [尋找](#locate-installer-files)伺服器作業系統的安裝程式。

### <a name="windows-machine"></a>視窗機

- 從命令提示符中,執行以下命令以將安裝程式複製到要保護的伺服器上的本地資料夾,如_C:_Temp。_ 將安裝程式的檔名替換為實際檔名。

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 運行此命令以安裝代理。

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 運行這些命令以將代理註冊到配置伺服器。

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>安裝設定

設定 | 詳細資料
--- | ---
語法 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
安裝記錄 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
`/InstallLocation`| 選擇性參數。 指定行動服務安裝位置 (任何資料夾)。
`/Platform` | Mandatory。 指定在其中安裝行動服務的平臺: <br/> **VMware** VM/物理伺服器。 <br/> **Azure** VM。<br/><br/> 如果要將 Azure VM 視為物理電腦,請指定**VMware**。
`/Silent`| 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

設定 | 詳細資料
--- | ---
語法 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
代理程式設定記錄 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 必要參數。 `<CSIP>`指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
`/PassphraseFilePath` |  Mandatory。 複雜密碼的位置。 請使用任何有效的 UNC 或本機檔案路徑。

### <a name="linux-machine"></a>Linux 機器

1. 從終端作業階段中,將安裝程式複製到要保護的伺服器上的 _/tmp_等本地資料夾。 將安裝程式的檔名替換為 Linux 發行版的實際檔名,然後運行這些命令。

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 依以下方式進行安裝：

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 安裝完成後,必須將行動服務註冊到配置伺服器。 運行以下命令以將行動服務註冊到配置伺服器。

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>安裝設定

設定 | 詳細資料
--- | ---
語法 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 必要安裝參數。 指定應該要安裝行動服務 (MS) 還是主要目標 (MT)。
`-d` | 選擇性參數。 指定行動服務安裝位置: `/usr/local/ASR`。
`-v` | Mandatory。 指定在其中安裝行動服務的平臺。 <br/> **VMware** VM/物理伺服器。 <br/> **Azure** VM。
`-q` | 選擇性。 指定是否要以無訊息模式執行安裝程式。

#### <a name="registration-settings"></a>註冊設定

設定 | 詳細資料
--- | ---
語法 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 必要參數。 `<CSIP>`指定設定伺服器的 IP 位址。 請使用任何有效的 IP 位址。
`-P` |  Mandatory。 儲存複雜密碼之檔案的完整檔案路徑。 請使用任何有效的資料夾。

## <a name="azure-virtual-machine-agent"></a>Azure 虛擬機器代理程式

- **Windows VM**：從行動服務 9.7.0.0 版開始，會由行動服務安裝程式安裝 [Azure VM 代理程式](/azure/virtual-machines/extensions/features-windows#azure-vm-agent)。 這可確保當電腦故障轉移到 Azure 時,Azure VM 滿足使用任何 VM 擴展的代理安裝先決條件。
- **Linux VM**：在容錯移轉之後，必須在 Azure VM 上手動安裝 [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="locate-installer-files"></a>尋找安裝程式檔案

在設定伺服器上移到資料夾 _%程式資料%\ASR_home_svsystems_pushinstallsvc_儲存函式庫_。 根據作業系統檢查您需要的安裝程式。 下表總結了每個 VMware VM 和實體伺服器作業系統的安裝程式檔。 在開始之前,您可以查看[支援的作業系統](vmware-physical-azure-support-matrix.md#replicated-machines)。

> [!NOTE]
> 檔名使用下表中顯示的語法,_其中版本_和_日期_作為實際值的佔位符。 實際檔案名將類似於以下範例:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

安裝程式檔案 | 作業系統 (僅限 64 位元)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | 紅帽企業 Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> 包括 SP2 和 SP3。
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | 甲骨文企業Linux 6.4 </br> 甲骨文企業Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 伺服器
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>後續步驟

[設定行動服務的推入安裝](vmware-azure-install-mobility-service.md)。
