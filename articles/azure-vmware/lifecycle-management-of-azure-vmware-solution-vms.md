---
title: Azure VMware 解決方案 Vm 的生命週期管理
description: 瞭解如何使用 Microsoft Azure 原生工具來管理 Azure VMware 解決方案 Vm 生命週期的所有層面。
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 452fa6bf4610c9d0dcb46587242e8d6df9cebd0d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950358"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware 解決方案 Vm 的生命週期管理

Microsoft Azure 原生工具可讓您監視和管理 Azure 環境中 (Vm) 的虛擬機器。 還可讓您監視和管理 Azure VMware 解決方案和內部部署 Vm 上的 Vm。 在此總覽中，我們將探討 Azure 提供的整合式監視架構，以及如何在整個生命週期中使用其原生工具來管理您的 Azure VMware 解決方案 Vm。

## <a name="benefits"></a>優點

- Azure 原生服務可以用來管理混合式環境中的 Vm， (Azure、Azure VMware 解決方案和內部部署) 。
- Azure、Azure VMware 解決方案和內部部署 Vm 的整合式監視與可見度。
- 您可以使用 Azure 自動化中的 Azure 更新管理，來管理 Windows 和 Linux 電腦的作業系統更新。 
- Azure 資訊安全中心提供先進的威脅防護，包括：
    - 檔案完整性監視
    - 無檔案安全性警示
    - 作業系統修補程式評估
    - 安全性設定錯誤的評量
    - 端點保護評量 
- 使用適用于新 Vm 的 Azure ARC，輕鬆地部署 Microsoft Monitoring Agent (MMA) 。 
- Azure 監視器中的 Log Analytics 工作區，可使用 MMA 或擴充功能收集記錄收集和效能計數器。 將資料和記錄收集到單一點，並將該資料呈現給不同的 Azure 原生服務。 
- Azure 監視器的額外優點包括： 
    - 無縫監視 
    - 更好的基礎結構可見度 
    - 立即通知 
    - 自動解決 
    - 成本效益 

## <a name="integrated-azure-monitoring-architecture"></a>整合式 Azure 監視架構

下圖顯示 Azure VMware 解決方案 Vm 的整合式監視架構。

![整合式 Azure 監視架構](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>整合和部署 Azure 原生服務

**AZURE ARC** 可將 azure 管理延伸到任何基礎結構，包括 Azure VMware 解決方案、內部部署或其他雲端平臺。 您可以在 Azure VMware 解決方案環境中安裝 Azure Kubernetes Service (AKS) 叢集，以部署 azure ARC。 如需詳細資訊，請參閱 [連接已啟用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/connect-cluster.md)叢集。

您可以透過 MMA (（也稱為 Log Analytics 代理程式或 OMS Linux 代理程式) ）來監視 Azure VMware 解決方案 Vm。 透過 ARC VM 生命週期工作流程布建 Vm 時，可以自動安裝 MMA。 從 vCenter 中的範本部署 Vm 時，也可以安裝 MMA。同樣地，透過 ARC 工作流程布建 Vm。 所有已布建的 Azure VMware 解決方案 Vm 接著可以安裝 MMA，並將記錄傳送至 Azure Log Analytics 工作區。 如需詳細資訊，請參閱 [Log Analytics 代理程式總覽](../azure-monitor/platform/log-analytics-agent.md)。

**Log Analytics 工作區** 會使用 MMA 或擴充功能來啟用記錄收集和效能計數器集合。 若要建立 Log Analytics 工作區，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。
- 若要將 Windows Vm 新增至您的 log analytics 工作區，請參閱 [在 Windows 電腦上安裝 Log analytics 代理程式](../azure-monitor/platform/agent-windows.md)。
- 若要將 Linux Vm 新增至您的 log analytics 工作區，請參閱 [在 Linux 電腦上安裝 Log analytics 代理程式](../azure-monitor/platform/agent-linux.md)。

Azure 自動化中的**Azure 更新管理**會在混合式環境中管理 Windows 和 Linux 電腦的作業系統更新。 它會監視修補合規性，並將修補偏差警示轉送到 Azure 監視器以進行補救。 Azure 更新管理必須連接到您的 Log Analytics 工作區，才能使用儲存的資料來評估 Vm 上的更新狀態。
- 若要將 Log Analytics 新增至 Azure 更新管理，您必須先 [建立 Azure 自動化帳戶](../automation/automation-create-standalone-account.md)。
- 若要將您的 Log Analytics 工作區與您的自動化帳戶連結，請參閱 [Log analytics 工作區和自動化帳戶](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)。
- 若要為您的 Vm 啟用 Azure 更新管理，請參閱 [從自動化帳戶啟用更新管理](../automation/update-management/update-mgmt-enable-automation-account.md)。
- 將 Vm 新增至 Azure 更新管理之後，您可以 [在 vm 上部署更新，並查看結果](../automation/update-management/update-mgmt-deploy-updates.md)。 

**Azure 資訊安全中心** 在雲端和內部部署的混合式工作負載之間提供先進的威脅防護。 它會評估 Azure VMware 解決方案 Vm 的弱點，並視需要提出警示。 這些安全性警示可以轉送到 Azure 監視器以進行解析。
- Azure 資訊安全中心不需要部署。 如需詳細資訊，請參閱 [虛擬機器支援的功能](../security-center/security-center-services.md)清單。
- 若要將 Azure VMware 解決方案 Vm 和非 Azure Vm 新增至 Azure 資訊安全中心，請參閱將 [Windows 電腦上架至 Azure 資訊安全中心](../security-center/quickstart-onboard-machines.md) ，並讓 [Linux 電腦上線至 Azure 資訊安全中心](../security-center/quickstart-onboard-machines.md)。
- 新增 Vm 之後，Azure 資訊安全中心會分析資源的安全性狀態，以找出潛在的弱點。 它也會在 [總覽] 索引標籤中提供建議。如需詳細資訊，請參閱 [Azure 資訊安全中心中的安全性建議](../security-center/security-center-recommendations.md)。
- 您可以在 Azure 資訊安全中心中定義安全性原則。 如需有關設定安全性原則的詳細資訊，請參閱 [使用安全性原則](../security-center/tutorial-security-policy.md)。

**Azure 監視器** 是一套完整的解決方案，可收集、分析及處理來自雲端和內部部署環境的遙測資料。 不需要部署。
- Azure 監視器可讓您收集來自不同來源的資料，以進行監視和分析。 如需詳細資訊，請參閱 [Azure 監視器的監視資料來源](../azure-monitor/platform/data-sources.md)。
- 您也可以收集不同類型的資料來進行分析、視覺效果和警示。 如需詳細資訊，請參閱 [Azure 監視器資料平臺](../azure-monitor/platform/data-platform.md)。
- 若要使用 Log Analytics 工作區設定 Azure 監視器，請參閱 [設定適用於 VM 的 Azure 監視器的 Log analytics 工作區](../azure-monitor/insights/vminsights-configure-workspace.md)。
- 您可以建立警示規則來識別環境中的問題，例如資源的高使用率、遺失的修補程式、磁碟空間不足，以及 Vm 的心跳。 您也可以將警示傳送至 IT 服務管理 (ITSM) 工具，以設定偵測到事件的自動回應。 警示偵測通知也可以透過電子郵件傳送。 若要建立這類規則，請參閱：
    - [使用 Azure 監視器建立、查看和管理計量警示](../azure-monitor/platform/alerts-metric.md)。
    - [使用 Azure 監視器建立、查看和記錄管理警示](../azure-monitor/platform/alerts-log.md)。
    - 設定自動化動作和通知的[動作規則](../azure-monitor/platform/alerts-action-rules.md)。
    - [使用 IT 服務管理連接器將 Azure 連接到 ITSM 工具](../azure-monitor/platform/itsmc-overview.md)。

**Azure 平臺即服務 (PaaS) ** 是雲端中的開發和部署環境，其資源可讓您提供雲端式應用程式。 例如，您可以將 Azure SQL Database 與您的 Azure VMware 解決方案 Vm 整合。 SQL 警示接著可能與 Azure VMware 解決方案 VM 警示相互關聯。 比方說，假設您的應用程式 SQL Database 階段是在 Azure PAAS 內，而相同應用程式的 web 應用層則裝載在您的 Azure VMware 解決方案 Vm 上。 然後，資料庫警示可以與 web 應用程式警示相互關聯。 針對 Azure、Azure VMware 解決方案和內部部署 Vm 的單一整合式可見度，簡化了疑難排解。