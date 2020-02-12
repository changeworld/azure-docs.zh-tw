---
title: Azure Automation State Configuration 概觀
description: Azure Automation State Configuration (DSC)、其條款和已知問題的概觀
keywords: powershell dsc, 需要的狀態組態, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850885"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概觀

Azure Automation State Configuration 是一種 Azure 服務，可讓您撰寫、管理和編譯 PowerShell Desired State Configuration (DSC) [組態](/powershell/scripting/dsc/configurations/configurations)、匯入 [DSC 資源](/powershell/scripting/dsc/resources/resources)，以及將組態指派給目標節點，而這些作業都是在雲端中進行。

## <a name="why-use-azure-automation-state-configuration"></a>為何使用 Azure Automation State Configuration

Azure Automation State Configuration 提供在 Azure 外部使用 DSC 的數個優點。

### <a name="built-in-pull-server"></a>內建提取伺服器

Azure Automation State Configuration 提供類似於 [Windows 功能 DSC-Service](/powershell/scripting/dsc/pull-server/pullserver) 的 DSC 提取伺服器，因此目標節點會自動接收組態、符合期望狀態，並回報其相容性。 Azure 自動化中的內建提取伺服器可讓您不需要設定和維護自己的提取伺服器。 Azure 自動化會以位於雲端或內部部署的虛擬或實體 Windows 或 Linux 機器為目標。

### <a name="management-of-all-your-dsc-artifacts"></a>所有 DSC 構件的管理

Azure Automation State Configuration 為 [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) 帶來與 Azure 自動化提供的 PowerShell 指令碼相同的管理層。

從 Azure 入口網站或 PowerShell 中，您可以管理所有 DSC 設定、資源和目標節點。

![Azure 自動化頁面的螢幕擷取畫面](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>將報告資料匯入 Azure 監視器記錄檔

使用 Azure Automation State Configuration 所管理的節點會將詳細報告狀態資料傳送至內建提取伺服器。 您可以設定 Azure Automation State Configuration，將此資料傳送至 Log Analytics 工作區。 若要瞭解如何將狀態設定狀態資料傳送至 Log Analytics 工作區，請參閱將[Azure 自動化狀態設定報告資料轉送至 Azure 監視器記錄](automation-dsc-diagnostics.md)。

## <a name="prerequisites"></a>必要條件

使用 Azure 自動化狀態設定（DSC）時，請考慮下列需求。

### <a name="operating-system-requirements"></a>作業系統需求

針對執行 Windows 的節點，支援下列版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) 獨立產品 SKU 不包含 Desired State Configuration 的實作，因此無法由 PowerShell DSC 或 Azure 自動化狀態設定管理。

針對執行 Linux 的節點，支援下列散發版本/版本：

DSC Linux 延伸模組支援[支援的 Linux 發行](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)版底下列出的所有 Linux 散發套件。

### <a name="dsc-requirements"></a>DSC 需求

針對在 Azure 中執行的所有 Windows 節點，將會在上架期間安裝[WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) 。  對於執行 Windows Server 2012 和 Windows 7 的節點，[將會啟用 WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

針對在 Azure 中執行的所有 Linux 節點，將會在上架期間安裝[POWERSHELL DSC For linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 。

### <a name="network-planning"></a>設定私人網路

如果您的節點位於私人網路內，則需要下列埠和 Url，狀態設定（DSC）才能與自動化進行通訊：

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取。
* 全域 URL：*.azure-automation.net
* US Gov 維吉尼亞州的全域 URL： *.azure automation.us
* 代理程式服務： https://\<workspaceId\>.agentsvc.azure-automation.net

這可讓受管理節點的網路連線與 Azure 自動化進行通訊。
如果您使用在節點之間進行通訊的 DSC 資源（例如[WaitFor * 資源](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)），您也必須允許節點之間的流量。
請參閱每個 DSC 資源的檔，以瞭解這些網路需求。

#### <a name="proxy-support"></a>Proxy 支援

DSC 代理程式的 Proxy 支援可在 Windows 1809 版和更新版本中取得。
若要設定此選項，請在用來註冊節點的中繼配置[腳本](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)中，設定**ProxyURL**和**ProxyCredential**的值。
在舊版 Windows 中，DSC 無法使用 Proxy。

針對 Linux 節點，DSC 代理程式支援 proxy，並會利用 HTTP_proxy 變數來判斷 url。

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure 狀態設定網路範圍和命名空間

建議使用定義例外狀況時所列出的位址。 針對 IP 位址，您可以下載 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄：

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美國東部   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。
>
> 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要深入了解如何將節點上架，請參閱[將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
