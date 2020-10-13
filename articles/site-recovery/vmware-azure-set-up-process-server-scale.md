---
title: 使用 Azure Site Recovery 在 VMware Vm 和實體伺服器的災難復原期間設定相應放大進程伺服器 |Microsoft Docs '
description: 本文說明如何在 VMware Vm 和實體伺服器的嚴重損壞修復期間設定相應放大進程伺服器。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292811"
---
# <a name="scale-with-additional-process-servers"></a>使用額外的進程伺服器進行調整

依預設，使用 [Site Recovery](site-recovery-overview.md) 複製 VMware VM 或實體伺服器至 Azure 時，處理序伺服器會安裝在設定伺服器電腦上，並且用於協調 Site Recovery 與內部部署基礎結構之間的資料轉送。 若要增加容量和相應放大您的複寫部署，您可以加入其他獨立處理序伺服器。 本文說明如何設定相應放大進程伺服器。

## <a name="before-you-start"></a>開始之前

### <a name="capacity-planning"></a>容量規劃

請確定您已執行 VMware 複寫的[容量規劃](site-recovery-plan-capacity-vmware.md)。 這有助於您識別如何及何時應該部署額外的處理序伺服器。

從9.24 版開始，在為新的複寫選擇進程伺服器時，會加入指引。 進程伺服器會根據特定準則標示為「狀況良好」、「警告」和「重大」。 若要瞭解可能影響處理伺服器狀態的不同案例，請參閱 [進程伺服器警示](vmware-physical-azure-monitor-process-server.md#process-server-alerts)。

> [!NOTE]
> 不支援使用複製的處理序伺服器元件。 請遵循本文中的步驟進行每個 PS 相應放大作業。

### <a name="sizing-requirements"></a>調整大小需求 

確認表格中的調整大小需求摘要。 一般來說，如果您必須將您的部署放大至超過 200 部來源機器，或是擁有總計超過 2 TB 的每日變換率，您便需要額外的處理序伺服器來處理流量。

| **額外處理序伺服器** | **快取磁碟大小** | **資料變更率** | **受保護的電腦** |
| --- | --- | --- | --- |
|4 個 vCPU (2 個插槽 * 2 核心 \@ 2.5 GHz)，8 GB 記憶體 |300 GB |250 GB 或更少 |複寫 85 部或更少的機器。 |
|8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5 GHz)，12 GB 記憶體 |600 GB |250 GB 至 1 TB |複寫 85-150 部機器。 |
|12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5 GHz)，24 GB 記憶體 |1 TB |1 TB 至 2 TB |複寫 150-225 部機器。 |

每個受保護的來源機器都會設定各 100 GB 的 3 個磁碟。

### <a name="prerequisites"></a>Prerequisites

下表為額外處理序伺服器的必要條件摘要。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>下載安裝檔

依照下列指示下載處理序伺服器的安裝檔案：

1. 登入 Azure 入口網站，然後流覽至您的復原服務保存庫。
2. 針對 vmware & 實體機器) ，開啟**Site Recovery 基礎結構**  >  **VMware 和實體機器**設定  >  **伺服器** (。
3. 選取設定伺服器以切入伺服器的詳細資料。 接著按一下** [+ 處理序伺服器]**。
4. 在 [**新增進程伺服器**] 中  >   ，**選擇您要部署進程伺服器的位置**，然後選取 [**在內部部署部署相應放大進程伺服器**]。

   ![新增伺服器頁面](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. 按一下 **[下載 Microsoft Azure Site Recovery 整合安裝]**。 這會下載最新版本的安裝檔案。

   > [!WARNING]
   > 處理序伺服器安裝的版本應該與您執行的設定伺服器版本相同或為較舊版本。 一個確保版本相容性的簡單方式，便是使用和您最近用來安裝或更新設定伺服器的安裝程式相同的安裝程式。

## <a name="install-from-the-ui"></a>從 UI 安裝

請依照以下指示安裝。 設定伺服器之後，請移轉來源機器以便使用它。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>從命令列安裝

執行下列命令進行安裝：

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

命令列參數如下所示：

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

例如：

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>建立 Proxy 設定檔

如果您需要設定 Proxy，ProxySettingsFilePath 參數會使用檔案做為輸入。 您可以依照下列指示建立檔案，並將它以輸入 ProxySettingsFilePath 參數傳遞。

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>接下來的步驟
深入了解[管理處理序伺服器設定](vmware-azure-manage-process-server.md)
