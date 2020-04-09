---
title: 為 VM 啟用 Azure 監視器概述
description: 瞭解如何為 VM 部署和配置 Azure 監視器。 了解系統要求。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982305"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>為 VM 啟用 Azure 監視器概述

本文概述了可用於在虛擬機器上啟用 Azure 監視器的 VM 以監視運行狀況和性能的選項。 發現在 Azure 虛擬機器 (VM) 和虛擬機器縮放集、本地 VM 或託管於另一個雲端環境中的 VM 上執行的應用程式依賴項。  

要為 VM 設定 Azure 監視器,可以執行:

* 通過直接從 VM 或虛擬機器規模集中選擇**見解**,啟用單個 Azure VM 或虛擬機器規模集。
* 使用 Azure 策略啟用兩個或多個 Azure VM 和虛擬機縮放集。 此方法可確保在現有和新的 VM 和縮放集上安裝所需的依賴項並正確配置。 報告不合規的 VM 和縮放集,因此您可以決定是否啟用它們並對其進行修復。
* 使用 PowerShell，跨指定的訂用帳戶或資源群組啟用兩個或多個 Azure VM 或虛擬機器擴展集。
* 啟用 VM 的 Azure 監視器來監視在公司網路或其他雲端環境中託管的 VM 或物理電腦。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您了解下列各節中的資訊。 

>[!NOTE]
>目前目前設定的以下資訊也適用於[服務映射解決方案](service-map.md)。  

### <a name="log-analytics"></a>Log Analytics

VM 的 Azure 監視器支援以下區域中的紀錄分析工作區:

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
>可以監視任何區域中的 Azure VM。 VM 本身並不限於日誌分析工作區支援的區域。
>

如果沒有日誌分析工作區,則可以使用以下資源之一創建一個工作區:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 門戶](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

您還可以在為 Azure 門戶中設置單個 Azure VM 或虛擬機器規模設定啟用監視時創建工作區。

要在紀錄分析工作區中設定使用 Azure 策略、Azure PowerShell 或 Azure 資源管理員樣本的縮放方案::

* 安裝*服務映射*和基礎設施*見解*解決方案。 可以使用提供的 Azure 資源管理器範本完成此安裝。 或者,在 Azure 門戶中的「**開始」** 選項卡上,選擇 **「配置工作區**」 。。
* 設定 Log Analytics 工作區，以收集效能計數器。

要為規模方案配置工作區,請使用以下方法之一:

* 使用[Azure 電源外殼](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在 VM[**策略覆蓋範圍**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)的 Azure 監視器頁上,選擇 **「配置工作區**」 。。 

### <a name="supported-operating-systems"></a>支援的作業系統

下表列出了 Azure 監視器支援的 VM Windows 和 Linux 作業系統。 在本節中的稍後部分,您將找到一個完整清單,詳細說明主要和次要的 Linux 操作系統版本和支援的內核版本。

|作業系統版本 |效能 |地圖 |
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
|紅帽企業 Linux (RHEL) 6, 7| X | X| 
|烏本圖 18.04, 16.04 | X | X |
|CentOS Linux 7、6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4、8 | X<sup>1</sup> | |

<sup>1</sup> 適用於 VM 的 Azure 監視器的效能功能僅能從 Azure 監視器使用。 它不能直接從 Azure VM 的左側窗格中可用。

>[!NOTE]
>在 Linux 作業系統中:
> - 只支援預設版本和 SMP Linux 核心版本。
> - 所有 Linux 散發套件皆不支援非標準的核心版本，例如實體位址擴充 (PAE) 和 Xen。 例如，不支援版本字串為 *2.6.16.21-0.8-xen* 的系統。
> - 不支援自定義內核,包括標準內核的重新編譯。
> - 支援 CentOSPlus 內核。
> - 必須修補 Linux 內核以尋找 Spectre 漏洞。 有關詳細資訊,請諮詢您的 Linux 發行版供應商。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| 作業系統版本 | 核心版本 |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| 作業系統版本 | 核心版本 |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| 作業系統版本 | 核心版本 |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| 作業系統版本 | 核心版本 |
|:--|:--|
| 18.04 | 5.0 (包括 Azure 調諧的內核)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| 作業系統版本 | 核心版本 |
|:--|:--|
|12 SP4 | 4.12.* (包括 Azure 調諧的內核) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| 作業系統版本 | 核心版本 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

VM Azure 監視器中的地圖功能從 Microsoft 依賴項代理獲取其數據。 Dependency Agent 須憑藉 Log Analytics 代理程式才能連線至 Log Analytics。 因此,您的系統必須安裝日誌分析代理並與依賴項代理一起配置。

無論是為單個 Azure VM 啟用 VM 的 Azure 監視器,還是使用大規模部署方法,請使用[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)或[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure VM 依賴項代理擴展作為體驗的一部分安裝代理。

>[!NOTE]
>目前目前設定的以下資訊也適用於[服務映射解決方案](service-map.md)。  

在混合環境中,您可以手動下載和安裝依賴項代理或使用自動方法。

下表說明對應功能在混合式環境中支援的連線來源。

| 連線的來源 | 支援 | 描述 |
|:--|:--|:--|
| Windows 代理程式 | 是 | Windows 代理與 Windows 的[日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)一起需要依賴項代理。 有關詳細資訊,請參閱[支援的作業系統](#supported-operating-systems)。 |
| Linux 代理程式 | 是 | 除了 Linux 的[日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)外,Linux 代理還需要依賴項代理。 有關詳細資訊,請參閱[支援的作業系統](#supported-operating-systems)。 |
| System Center Operations Manager 管理群組 | 否 | |

可以從以下位置下載相依項代理:

| 檔案 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE99F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9EE00D54AA7F0b |

## <a name="role-based-access-control"></a>角色型存取控制

要啟用和訪問 AZURE 監視器中的 VM 功能,必須具有*日誌分析參與者*角色。 要查看性能、運行狀況和地圖數據,必須具有 Azure VM 的*監視讀取器*角色。 您必須針對適用於 VM 的 Azure 監視器設定 Log Analytics 工作區。

如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms"></a>如何為 VM 啟用 Azure 監視器

使用下表中描述的方法之一為 VM 啟用 Azure 監視器:

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 單個 Azure VM 或虛擬機器縮放集 | [從 VM 啟用](vminsights-enable-single-vm.md) | 您可以通過直接從 VM 或虛擬機器規模集中選擇**見解**來啟用單個 Azure VM。 |
| 多個 Azure VM 或虛擬機器縮放集 | [透過 Azure 政策開啟](vminsights-enable-at-scale-policy.md) | 可以使用 Azure 策略和可用的策略定義啟用多個 Azure VM。 |
| 多個 Azure VM 或虛擬機器縮放集 | [透過 Azure PowerShell 或 Azure 資源管理員樣本啟用](vminsights-enable-at-scale-powershell.md) | 通過使用 Azure PowerShell 或 Azure 資源管理器範本,可以在指定的訂閱或資源群組中啟用多個 Azure VM 或虛擬機縮放集。 |
| 混合式雲端 | [為混合環境開啟](vminsights-enable-hybrid-cloud.md) | 您可以部署到資料中心或其他雲端環境中託管的 VM 或物理電腦。 |

## <a name="management-packs"></a>管理組件

啟用 VM 的 Azure 監視器並設定日誌分析工作區時,管理包將轉發到報告該工作區的所有 Windows 計算機。 如果將[系統中心操作管理員管理組](../../azure-monitor/platform/om-agents.md)與日誌分析工作區集成,則服務映射管理包將從管理組部署到向管理組報告的 Windows 電腦。  

管理包名為*微軟.智慧包.應用程式依賴監視器*。 它寫入`%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`資料夾。 管理套件使用的資料來源`%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`是 。

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料

當您使用 Azure 監視器服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 使用此數據來提高服務的品質、安全性和完整性。 

為了提供準確、高效的故障排除功能,Map 功能包含有關軟體配置的數據。 數據提供作業系統和版本、IP 位址、DNS 名稱和工作站名稱等資訊。 Microsoft 不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

現在,您已為 VM 啟用了監視,監視資訊可在 Azure 監視器中用於 VM 的分析。

## <a name="next-steps"></a>後續步驟

要瞭解如何使用效能監視功能,請參閱[檢視 Azure 監視器以瞭解 VM 效能](vminsights-performance.md)。 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。
