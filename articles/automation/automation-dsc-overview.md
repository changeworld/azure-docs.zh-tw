---
title: Azure Automation State Configuration 概觀
description: 本文提供 Azure Automation State Configuration 的概觀。
keywords: powershell dsc, 需要的狀態組態, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9880915061c0639aebe30bdb33258d7c79e155d7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836884"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概觀

Azure Automation State Configuration 是一種 Azure 組態管理服務，可讓您針對任何雲端或內部部署資料中心內的節點撰寫、管理和編譯 PowerShell Desired State Configuration (DSC) [組態](/powershell/scripting/dsc/configurations/configurations)。 此服務也會匯入 [DSC 資源](/powershell/scripting/dsc/resources/resources)，並將組態指派給雲端中的所有目標節點。 您可以在 [組態管理] 底下選取 [State Configuration (DSC)]，以存取 Azure 入口網站中的 Azure Automation State Configuration。 

您可以使用 Azure Automation State Configuration 來管理各種不同的機器：

- Azure 虛擬機器
- Azure 虛擬機器 (傳統)
- 位於內部部署或 Azure (包括 AWS EC2 執行個體) 以外雲端中的實體/虛擬 Windows 機器
- 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

如果您不準備從雲端管理機器組態，則可以使用 Azure Automation State Configuration 當作報告專用端點。 這項功能可讓您透過 DSC 設定 (推送) 組態，並在 Azure 自動化中檢視報告詳細資料。

> [!NOTE]
> 如果已安裝的 Azure VM Desired State Configuration 延伸模組版本大於 2.70，則可以使用 Azure Automation State Configuration 管理 Azure VM，不需要額外付費。 如需詳細資訊，請參閱[**自動化定價頁面**](https://azure.microsoft.com/pricing/details/automation/)。

## <a name="why-use-azure-automation-state-configuration"></a>為何使用 Azure Automation State Configuration

Azure Automation State Configuration 提供在 Azure 外部使用 DSC 的數個優點。 此服務可讓您從中央、安全的位置快速且輕鬆地延展到數千部電腦。 您可以輕鬆地啟用機器、將宣告式組態指派給這些機器，以及檢視報告，其中顯示每部機器是否符合您指定的所需狀態。

Azure Automation State Configuration 服務之於 DSC 如同 Azure 自動化 Runbook 之於 PowerShell 指令碼。 換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。 

### <a name="built-in-pull-server"></a>內建提取伺服器

Azure Automation State Configuration 提供類似於 [Windows 功能 DSC 服務](/powershell/scripting/dsc/pull-server/pullserver)的 DSC 提取伺服器。 目標節點會自動接收組態、符合所需狀態，並回報其合規性。 Azure 自動化中的內建提取伺服器可讓您不需要設定和維護自己的提取伺服器。 Azure 自動化會以位於雲端或內部部署的虛擬或實體 Windows 或 Linux 機器為目標。

### <a name="management-of-all-your-dsc-artifacts"></a>所有 DSC 構件的管理

Azure Automation State Configuration 為 [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) 帶來與其提供給 PowerShell 指令碼相同的管理層。 從 Azure 入口網站或 PowerShell 中，您可以管理所有 DSC 組態、資源和目標節點。

![Azure 自動化頁面的螢幕擷取畫面](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>將報告資料匯入至 Azure 監視器記錄

使用 Azure Automation State Configuration 所管理的節點會將詳細報告狀態資料傳送至內建提取伺服器。 您可以設定 Azure Automation State Configuration，將此資料傳送至 Log Analytics 工作區。 請參閱[將 Azure Automation State Configuration 報告資料轉送至 Azure 監視器記錄](automation-dsc-diagnostics.md)。

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>使用 Azure Automation State Configuration 的必要條件

使用 Azure Automation State Configuration 時，請考慮本節中的需求。

### <a name="operating-system-requirements"></a>作業系統需求

若為執行 Windows 的節點，支援下列版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-v Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) 獨立產品 SKU 並未包含 DSC 的實作。 因此，其無法由 PowerShell DSC 或 Azure Automation State Configuration 管理。

若為執行 Linux 的節點，DSC Linux 延伸模組支援所有在[支援的 Linux 發行版本](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)底下列出的 Linux 發行版本。

### <a name="dsc-requirements"></a>DSC 需求

若為在 Azure 中執行的所有 Windows 節點，當啟用機器時，會安裝 [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure)。 若為執行 Windows Server 2012 和 Windows 7 的節點，會啟用 [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

若為在 Azure 中執行的所有 Linux 節點，當啟用機器時，會安裝 [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>私人網路的組態

如果您的節點位於私人網路中，則需要下列連接埠和 URL。 這些資源會為受控節點提供網路連線能力，並允許 DSC 與 Azure 自動化進行通訊。

* 連接埠︰只需要 TCP 443，即可存取輸出網際網路
* 全域 URL： ***.azure-automation.net**
* US Gov 維吉尼亞州的全域 URL： ***.azure automation.us**
* 代理程式服務：**https://\<workspaceId\>.agentsvc.azure-automation.net**

如果您使用在節點之間進行通訊的 DSC 資源 (例如 [WaitFor* 資源](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource))，則也必須允許節點之間的流量。 請參閱每個 DSC 資源的文件，以了解這些網路需求。

#### <a name="proxy-support"></a>Proxy 支援

DSC 代理程式的 Proxy 支援適用於 Windows 版本 1809 和更新版本。 若要啟用此選項，您可以在用來註冊節點的[中繼組態指令碼](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)中設定 `ProxyURL` 和 `ProxyCredential` 屬性的值。 

>[!NOTE]
>Azure Automation State Configuration 不會為舊版 Windows 提供 DSC Proxy 支援。

若為 Linux 節點，DSC 代理程式支援 Proxy，並使用 `http_proxy` 變數來判斷 URL。 若要深入了解 Proxy 支援，請參閱[產生 DSC 中繼組態](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)。

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration 網路範圍和命名空間

建議在定義例外狀況時使用底下列出的位址。 針對 IP 位址，您可以下載 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

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
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 

Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何啟用節點，請參閱[啟用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何編譯 DSC 組態，以便可將其指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的 DSC 組態](automation-dsc-compile.md)。
- 如需在持續部署管道中使用 Azure Automation State Configuration 的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 如需定價資訊，請參閱 [Azure Automation State Configuration 定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。