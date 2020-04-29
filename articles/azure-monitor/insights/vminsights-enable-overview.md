---
title: 啟用適用於 VM 的 Azure 監視器總覽
description: 瞭解如何部署和設定適用於 VM 的 Azure 監視器。 瞭解系統需求。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982305"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>啟用適用於 VM 的 Azure 監視器總覽

本文概述可用來在虛擬機器上啟用適用於 VM 的 Azure 監視器以監視健康情況和效能的選項。 探索在 Azure 虛擬機器（Vm）和虛擬機器擴展集、內部部署 Vm 或裝載于另一個雲端環境中的 Vm 上執行的應用程式相依性。  

若要設定適用於 VM 的 Azure 監視器：

* 直接從 VM 或虛擬機器擴展集選取 [**深入**解析]，以啟用單一 Azure VM 或虛擬機器擴展集。
* 使用 Azure 原則啟用兩個或多個 Azure Vm 和虛擬機器擴展集。 這個方法可確保在現有和新的 Vm 和擴展集上，已安裝並正確設定必要的相依性。 系統會報告不符合規範的 Vm 和擴展集，因此您可以決定是否要啟用它們並加以補救。
* 使用 PowerShell，跨指定的訂用帳戶或資源群組啟用兩個或多個 Azure VM 或虛擬機器擴展集。
* 啟用適用於 VM 的 Azure 監視器來監視在公司網路或其他雲端環境中裝載的 Vm 或實體電腦。

## <a name="prerequisites"></a>先決條件

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
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

當您在 Azure 入口網站中啟用單一 Azure VM 或虛擬機器擴展集的監視功能時，您也可以建立工作區。

若要在您的 Log Analytics 工作區中設定使用 Azure 原則、Azure PowerShell 或 Azure Resource Manager 範本的大規模案例：

* 安裝*ServiceMap*和*InfrastructureInsights*解決方案。 您可以使用提供的 Azure Resource Manager 範本來完成這項安裝。 或在 Azure 入口網站的 [**開始**使用] 索引標籤上，選取 [**設定工作區**]。
* 設定 Log Analytics 工作區，以收集效能計數器。

若要針對大規模案例設定您的工作區，請使用下列其中一種方法：

* 使用[Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在 [適用於 VM 的 Azure 監視器[**原則涵蓋範圍**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)] 頁面上，選取 [**設定工作區**]。 

### <a name="supported-operating-systems"></a>支援的作業系統

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
|Red Hat Enterprise Linux （RHEL）6、7| X | X| 
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
| 18.04 | 5.0 （包含 Azure 微調的核心）<br>4.18*<br>4.15* |
| 16.04.3 | 4.15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 核心版本 |
|:--|:--|
|12 SP4 | 4.12. * （包含 Azure 微調的核心） |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 版本 | 核心版本 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

適用於 VM 的 Azure 監視器中的對應功能會從 Microsoft Dependency agent 取得其資料。 Dependency Agent 須憑藉 Log Analytics 代理程式才能連線至 Log Analytics。 因此，您的系統必須已安裝 Log Analytics 代理程式，並使用 Dependency agent 進行設定。

無論您為單一 Azure VM 啟用適用於 VM 的 Azure 監視器，或使用大規模部署方法，請使用適用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)或[LINUX](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure VM Dependency agent 擴充功能，將代理程式安裝為體驗的一部分。

>[!NOTE]
>本節所述的下列資訊也適用于[服務對應解決方案](service-map.md)。  

在混合式環境中，您可以手動下載並安裝 Dependency agent，或使用自動化的方法。

下表說明對應功能在混合式環境中支援的連線來源。

| 連線的來源 | 支援 | 描述 |
|:--|:--|:--|
| Windows 代理程式 | 是 | 除了[適用于 windows 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，windows 代理程式還需要 Dependency agent。 如需詳細資訊，請參閱[支援的作業系統](#supported-operating-systems)。 |
| Linux 代理程式 | 是 | 除了適用于[linux 的 Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)，linux 代理程式還需要 Dependency agent。 如需詳細資訊，請參閱[支援的作業系統](#supported-operating-systems)。 |
| System Center Operations Manager 管理群組 | 否 | |

您可以從下列位置下載 Dependency agent：

| 檔案 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0B |

## <a name="role-based-access-control"></a>角色型存取控制

若要啟用和存取適用於 VM 的 Azure 監視器中的功能，您必須擁有*Log Analytics 參與者*角色。 若要查看效能、健康情況和對應資料，您必須擁有 Azure VM 的 [*監視讀取*者] 角色。 您必須針對適用於 VM 的 Azure 監視器設定 Log Analytics 工作區。

如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms"></a>如何啟用適用於 VM 的 Azure 監視器

使用下表所述的其中一種方法來啟用適用於 VM 的 Azure 監視器：

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 單一 Azure VM 或虛擬機器擴展集 | [從 VM 啟用](vminsights-enable-single-vm.md) | 您可以直接從 VM 或虛擬機器擴展集選取 [**見解**] 來啟用單一 Azure VM。 |
| 多個 Azure Vm 或虛擬機器擴展集 | [透過 Azure 原則啟用](vminsights-enable-at-scale-policy.md) | 您可以使用 Azure 原則和可用的原則定義來啟用多個 Azure Vm。 |
| 多個 Azure Vm 或虛擬機器擴展集 | [透過 Azure PowerShell 或 Azure Resource Manager 範本啟用](vminsights-enable-at-scale-powershell.md) | 您可以使用 Azure PowerShell 或 Azure Resource Manager 範本，在指定的訂用帳戶或資源群組上啟用多個 Azure Vm 或虛擬機器擴展集。 |
| 混合式雲端 | [針對混合式環境啟用](vminsights-enable-hybrid-cloud.md) | 您可以部署至您的資料中心或其他雲端環境中裝載的 Vm 或實體電腦。 |

## <a name="management-packs"></a>管理組件

當適用於 VM 的 Azure 監視器啟用並設定 Log Analytics 工作區時，會將管理元件轉送到向該工作區報告的所有 Windows 電腦。 如果您已將[System Center Operations Manager 管理群組](../../azure-monitor/platform/om-agents.md)與 Log Analytics 工作區整合，則服務對應管理元件會從管理群組部署到向管理群組報告的 Windows 電腦。  

此管理元件名為*microsoft.intelligencepacks.applicationdependencymonitor. microsoft.intelligencepacks.applicationdependencymonitor*。 其寫入`%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`資料夾。 管理元件使用的資料來源是`%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`。

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料

當您使用 Azure 監視器服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 會使用此資料來改善服務的品質、安全性和完整性。 

為了提供正確且有效率的疑難排解功能，「對應」功能包含有關軟體設定的資料。 資料提供的資訊包括作業系統和版本、IP 位址、DNS 名稱和工作站名稱。 Microsoft 不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

既然您已啟用 VM 的監視功能，監視資訊就可以在適用於 VM 的 Azure 監視器中進行分析。

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用效能監視功能，請參閱[View 適用於 VM 的 Azure 監視器 performance](vminsights-performance.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。
