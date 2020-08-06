---
title: 啟用適用於 VM 的 Azure 監視器總覽
description: 瞭解如何部署和設定適用於 VM 的 Azure 監視器。 瞭解系統需求。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: e3c5f6d7e04620cf36f6cd952467d47afd775b19
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824761"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>啟用適用於 VM 的 Azure 監視器總覽

本文概述可用來啟用適用於 VM 的 Azure 監視器來監視下列各項健全狀況和效能的選項：

- Azure 虛擬機器 
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器
- 內部部署虛擬機器
- 裝載于另一個雲端環境中的虛擬機器。  

若要設定適用於 VM 的 Azure 監視器：

* 直接從 Azure 入口網站中的功能表選取 [**見解**]，以啟用單一 Azure VM、azure VMSS 或 azure Arc 機器。
* 使用 Azure 原則來啟用多個 Azure Vm、Azure VMSS 或 Azure Arc 機器。 這個方法可確保在現有和新的 Vm 和擴展集上，已安裝並正確設定必要的相依性。 系統會報告不符合規範的 Vm 和擴展集，因此您可以決定是否要啟用它們並加以補救。
* 使用 PowerShell，在指定的訂用帳戶或資源群組上啟用多個 Azure Vm、Azure Arc Vm、Azure VMSS 或 Azure Arc 機器。
* 啟用適用於 VM 的 Azure 監視器來監視在公司網路或其他雲端環境中裝載的 Vm 或實體電腦。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您了解下列各節中的資訊。 

>[!NOTE]
>本節所述的下列資訊也適用于[服務對應解決方案](service-map.md)。  

### <a name="log-analytics"></a>Log Analytics

適用於 VM 的 Azure 監視器支援下欄區域中的 Log Analytics 工作區：

- 美國中西部
- 美國西部
- 美國西部 2
- 美國中南部
- 美國東部
- 美國東部 2
- 美國中部
- 美國中北部
- US Gov Az
- US Gov Va
- 加拿大中部
- 英國南部
- 北歐
- 西歐
- 東亞
- 東南亞
- 印度中部
- 日本東部
- 澳大利亞東部
- 澳大利亞東南部

>[!NOTE]
>您可以監視任何區域中的 Azure Vm。 Vm 本身不限於 Log Analytics 工作區支援的區域。
>

如果您沒有 Log Analytics 工作區，您可以使用下列其中一個資源來建立一個：
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure 入口網站](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器

## <a name="supported-operating-systems"></a>支援的作業系統

下表列出適用於 VM 的 Azure 監視器支援的 Windows 和 Linux 作業系統。 在本節稍後，您會看到完整的清單，詳述主要和次要 Linux OS 版本，以及支援的核心版本。

|OS 版本 |效能 |地圖 |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6、7| X | X| 
|Ubuntu 18.04、16.04 | X | X |
|CentOS Linux 7、6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4、8 | X<sup>1</sup> | |

<sup>1</sup> 適用於 VM 的 Azure 監視器的效能功能僅能從 Azure 監視器使用。 它無法直接從 Azure VM 的左窗格中使用。

>[!NOTE]
>在 Linux 作業系統中：
> - 只支援預設版本和 SMP Linux 核心版本。
> - 所有 Linux 散發套件皆不支援非標準的核心版本，例如實體位址擴充 (PAE) 和 Xen。 例如，不支援版本字串為 *2.6.16.21-0.8-xen* 的系統。
> - 不支援自訂核心，包括標準核心的重新編譯。
> - 支援 CentOSPlus 核心。
> - Linux 核心必須針對 Spectre 弱點進行修補。 如需詳細資訊，請參閱您的 Linux 散發廠商。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 核心版本 |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 核心版本 |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| OS 版本 | 核心版本 |
|:--|:--|
| 6.10 | 2.6.32-754.3。5<br>2.6.32-696.30。1 |
| 6.9 | 2.6.32-696.30。1<br>2.6.32-696.18。7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 核心版本 |
|:--|:--|
| 18.04 | 5.3.0-1020<br>5.0 (包含 Azure 微調的核心) <br>4.18* <br> 4.15* |
| 16.04.3 | 4.15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 核心版本 |
|:--|:--|
|12 SP4 | 4.12. * (包含 Azure 微調的核心)  |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 版本 | 核心版本 |
|:--|:--|
| 9 | 4.9 | 

## <a name="supported-azure-arc-machines"></a>支援的 Azure Arc 機器
適用於 VM 的 Azure 監視器適用于可使用 Arc 擴充服務之區域中已啟用 Azure Arc 的伺服器。 您必須執行 Arc 代理程式的0.9 版或更新版本。

| 連線的來源 | 支援 | 描述 |
|:--|:--|:--|
| Windows 代理程式 | 是 | 除了[適用于 windows 的 Log Analytics 代理程式](../platform/log-analytics-agent.md)，windows 代理程式還需要 Dependency agent。 如需詳細資訊，請參閱[支援的作業系統](#supported-operating-systems)。 |
| Linux 代理程式 | 是 | 除了適用于[linux 的 Log Analytics 代理程式](../platform/log-analytics-agent.md)，linux 代理程式還需要 Dependency agent。 如需詳細資訊，請參閱[支援的作業系統](#supported-operating-systems)。 |
| System Center Operations Manager 管理群組 | 否 | |

## <a name="agents"></a>代理程式
適用於 VM 的 Azure 監視器需要在要監視的每個虛擬機器或虛擬機器擴展集上安裝下列兩個代理程式。 安裝這些代理程式並將其連接到工作區，是將資源上架的唯一需求。

- [Log Analytics 代理程式](../platform/log-analytics-agent.md)。 收集虛擬機器或虛擬機器擴展集的事件和效能資料，並將它傳遞至 Log Analytics 工作區。 適用于 Azure 資源上的 Log Analytics 代理程式的部署方法會使用適用于[Windows](../../virtual-machines/extensions/oms-windows.md)和[Linux](../../virtual-machines/extensions/oms-linux.md)的 VM 擴充功能。
- Dependency agent。 收集在虛擬機器上執行之進程和外部進程相依性（由[適用於 VM 的 Azure 監視器的對應功能](vminsights-maps.md)所使用）的已探索資料。 相依性代理程式會依賴 Log Analytics 代理程式，將其資料傳遞給 Azure 監視器。 適用于 Azure 資源上的相依性代理程式的部署方法會使用適用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 VM 擴充功能。

> [!NOTE]
> Log Analytics 代理程式是 System Center Operations Manager 所使用的相同代理程式。 適用於 VM 的 Azure 監視器可以監視也受 Operations Manager 監視的代理程式（如果它們是直接連線的），而且您會在其上安裝 Dependency agent。 透過[管理群組](../tform/../platform/om-agents.md)連線連接到 Azure 監視器的代理程式無法由適用於 VM 的 Azure 監視器監視。

以下是部署這些代理程式的多個方法。 

| 方法 | 描述 |
|:---|:---|
| [Azure 入口網站](./vminsights-enable-portal.md) | 在單一虛擬機器、虛擬機器擴展集或與 Azure Arc 連線的混合式虛擬機器上，安裝這兩個代理程式。 |
| [Resource Manager 範本](vminsights-enable-powershell.md) | 使用任何一種支援的方法來安裝這兩個代理程式，以部署包含 CLI 和 PowerShell 的 Resource Manager 範本。 |
| [Azure 原則](./vminsights-enable-policy.md) | 指派 Azure 原則計畫，以便在建立虛擬機器或虛擬機器擴展集時，自動安裝代理程式。 |
| [手動安裝](./vminsights-enable-hybrid.md) | 將代理程式安裝在 Azure 外部託管的電腦（包括您的資料中心或其他雲端環境）中的客體作業系統。 |




## <a name="management-packs"></a>管理組件
當 Log Analytics 工作區設定為適用於 VM 的 Azure 監視器時，會將兩個管理元件轉送到所有連線到該工作區的 Windows 電腦。 管理元件名為*microsoft.intelligencepacks.applicationdependencymonitor. microsoft.intelligencepacks.applicationdependencymonitor*和*microsoft.intelligencepacks.applicationdependencymonitor. VMInsights* ，並會寫入 *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service state\management packs pack \* 。 

*Microsoft.intelligencepacks.applicationdependencymonitor*管理元件所使用的資料來源是 **% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*。 *VMInsights*管理元件所使用的資料來源是 *% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*。

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料

當您使用 Azure 監視器服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 會使用此資料來改善服務的品質、安全性和完整性。 

為了提供正確且有效率的疑難排解功能，「對應」功能包含有關軟體設定的資料。 資料提供的資訊包括作業系統和版本、IP 位址、DNS 名稱和工作站名稱。 Microsoft 不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用效能監視功能，請參閱[View 適用於 VM 的 Azure 監視器 performance](vminsights-performance.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。