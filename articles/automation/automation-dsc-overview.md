---
title: Azure 自動化狀態設定概述
description: Azure 自動化狀態配置 (DSC)、其術語和已知問題的概述
keywords: powershell dsc, 需要的狀態組態, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: afceb11180662416aa4953b8b58ef03ffaa70eec
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383193"
---
# <a name="state-configuration-overview"></a>State Configuration 概觀

Azure 自動化狀態設定是 Azure 服務,允許您編寫、管理和編譯 PowerShell 所需的狀態設定 (DSC)[設定](/powershell/scripting/dsc/configurations/configurations)。 該服務還導入[DSC資源](/powershell/scripting/dsc/resources/resources),並將配置分配給目標節點,所有這些節點都在雲中。

## <a name="why-use-azure-automation-state-configuration"></a>為何使用 Azure Automation State Configuration

Azure Automation State Configuration 提供在 Azure 外部使用 DSC 的數個優點。

### <a name="built-in-pull-server"></a>內建提取伺服器

Azure 自動化狀態配置提供類似於[Windows 功能 DSC 服務的](/powershell/scripting/dsc/pull-server/pullserver)DSC 拉取伺服器。 目標節點可以自動接收配置、符合所需狀態並報告其合規性。 Azure 自動化中的內建提取伺服器可讓您不需要設定和維護自己的提取伺服器。 Azure 自動化會以位於雲端或內部部署的虛擬或實體 Windows 或 Linux 機器為目標。

### <a name="management-of-all-your-dsc-artifacts"></a>所有 DSC 構件的管理

Azure 自動化狀態配置將相同的管理層引入[PowerShell 所需狀態配置](/powershell/scripting/dsc/overview/overview),因為它為 PowerShell 腳本提供了相同的管理層。 從 Azure 門戶或 PowerShell,可以管理所有 DSC 配置、資源和目標節點。

![Azure 自動化頁面的螢幕擷取畫面](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>匯入 Azure 監視器紀錄

使用 Azure Automation State Configuration 所管理的節點會將詳細報告狀態資料傳送至內建提取伺服器。 您可以設定 Azure Automation State Configuration，將此資料傳送至 Log Analytics 工作區。 請參考[Azure 自動化狀態設定報告資料轉送到 Azure 監視器紀錄](automation-dsc-diagnostics.md)。

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>使用 Azure 自動化狀態設定的先決條件

在使用 DSC 的 Azure 自動化狀態配置時,請考慮以下要求。

### <a name="operating-system-requirements"></a>作業系統需求

對於執行 Windows 的節點,支援以下版本:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[微軟超 V 伺服器](/windows-server/virtualization/hyper-v/hyper-v-server-2016)獨立產品 SKU 不包含 DSC 的實現。 因此,它不能由 PowerShell DSC 或 Azure 自動化狀態配置進行管理。

對於運行 Linux 的節點,DSC Linux 擴充程式支援支援[Linux 發行版](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)下列出的所有 Linux 發行版。

### <a name="dsc-requirements"></a>DSC 要求

對於在 Azure 中運行的所有 Windows 節點[,WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure)在載入期間安裝。 對於運行 Windows 伺服器 2012 和 Windows 7 的節點,[啟用 WinRM。](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)

對於在 Azure 中運行的所有 Linux 節點,在載入過程中將安裝[適用於 Linux 的 PowerShell DSC。](https://github.com/Microsoft/PowerShell-DSC-for-Linux)

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>專用網路設定

如果節點位於專用網路中,則需要以下埠和 URL。 這些資源為託管節點提供網路連接,並允許 DSC 與 Azure 自動化通訊。

* 連接埠:僅需要 TCP 443 即可進行出站網路訪問
* 全域網址: =**.azure-automation.net**
* 美國弗吉尼亞州州長全球網址: •**.azure-automation.us**
* 代理服務 **:https://\<\>工作區 Id .agentsvc.azure-automation.net**

如果使用在節點之間通信的 DSC 資源(如[WaitFor® 資源](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)),則還需要允許節點之間的流量。 請參閱每個 DSC 資源的文檔以瞭解這些網路要求。

#### <a name="proxy-support"></a>代理支援

DSC 代理的代理支援在 Windows 版本 1809 及更高版本中可用。 此選項通過設置用於註冊節點的[元配置腳本](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)`ProxyURL`和`ProxyCredential`的值來啟用。

>[!NOTE]
>Azure 自動化狀態配置不為早期版本的 Windows 提供 DSC 代理支援。

對於 Linux 節點,DSC 代理支援`http_proxy`代理並使用 變數來確定 URL。

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure 自動化狀態設定網路範圍與命名空間

在定義異常時,建議使用下面列出的位址。 IP 位址,可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄：

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美國東部    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。
>
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 最好是每週下載新的 XML 檔案。 然後更新網站以正確標識在 Azure 中運行的服務。 

Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="next-steps"></a>後續步驟

- 要開始在 Azure 自動化狀態設定中使用 DSC,請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 要瞭解如何載入節點,請參閱[載入電腦來進行 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md)。
- 要瞭解如何編譯 DSC 設定以便將它們分配給目標節點,請參閱[在 Azure 自動化狀態設定中編譯設定](automation-dsc-compile.md)。
- 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有關定價資訊,請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 要檢視在連續部署導管中使用 Azure 自動化狀態設定的範例,請參考[Azure 自動化狀態設定和巧克力的持續部署](automation-dsc-cd-chocolatey.md)。
