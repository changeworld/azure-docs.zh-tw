---
title: Azure Automation State Configuration 概觀
description: 本文提供 Azure Automation State Configuration 的概觀。
keywords: powershell dsc, 需要的狀態組態, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 06/22/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6a1c6bb39e743a96ad110a60e41cc59306e7a2ae
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186379"
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

## <a name="prerequisites"></a>先決條件

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

若為在 Azure 中執行的所有 Windows 節點，當啟用機器時，會安裝 [WMF 5.1](/powershell/scripting/wmf/setup/install-configure)。 若為執行 Windows Server 2012 和 Windows 7 的節點，會啟用 [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

若為在 Azure 中執行的所有 Linux 節點，當啟用機器時，會安裝 [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>私人網路的組態

如果您的節點位於私人網路中，則需要下列連接埠和 URL。 這些資源會為受控節點提供網路連線能力，並允許 DSC 與 Azure 自動化進行通訊。

* 連接埠︰只需要 TCP 443，即可存取輸出網際網路
* 全域 URL： ***.azure-automation.net**
* US Gov 維吉尼亞州的全域 URL： ***.azure automation.us**
* Agent 服務： **HTTPs:// \<workspaceId\> . agentsvc.azure-automation.net**

如果您使用在節點之間進行通訊的 DSC 資源 (例如 [WaitFor* 資源](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource))，則也必須允許節點之間的流量。 請參閱每個 DSC 資源的文件，以了解這些網路需求。

若要瞭解 TLS 1.2 的用戶端需求，請參閱[Azure 自動化的 tls 1.2 強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

#### <a name="proxy-support"></a>Proxy 支援

DSC 代理程式的 Proxy 支援適用於 Windows 版本 1809 和更新版本。 若要啟用此選項，您可以在用來註冊節點的[中繼組態指令碼](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)中設定 `ProxyURL` 和 `ProxyCredential` 屬性的值。 

>[!NOTE]
>Azure Automation State Configuration 不會為舊版 Windows 提供 DSC Proxy 支援。

若為 Linux 節點，DSC 代理程式支援 Proxy，並使用 `http_proxy` 變數來判斷 URL。 若要深入了解 Proxy 支援，請參閱[產生 DSC 中繼組態](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)。

#### <a name="dns-records-per-region"></a>每個區域的 DNS 記錄

在定義例外狀況時，建議使用 [[每個區域的 DNS 記錄](how-to/automation-region-dns-records.md)] 資料表中所列的位址。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何啟用節點，請參閱[啟用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何編譯 DSC 組態，以便可將其指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的 DSC 組態](automation-dsc-compile.md)。
- 如需在持續部署管道中使用 Azure Automation State Configuration 的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 如需定價資訊，請參閱 [Azure Automation State Configuration 定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
