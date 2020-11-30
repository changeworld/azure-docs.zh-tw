---
title: Azure VMware 解決方案 Vm 的生命週期管理
description: 瞭解如何使用 Microsoft Azure 原生工具來管理 Azure VMware 解決方案 Vm 生命週期的所有層面。
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: ff1032900fa936895f3adfcb0d8a872f24948aca
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326806"
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
- 使用已啟用 Azure Arc 的伺服器 VM 延伸模組支援，輕鬆地為新的和現有的 Vm 部署 Log Analytics 代理程式。 
- Azure 監視器中的 Log Analytics 工作區，可使用 Log Analytics 代理程式或擴充功能來收集記錄收集和效能計數器。 將資料和記錄收集到單一點，並將該資料呈現給不同的 Azure 原生服務。 
- Azure 監視器的額外優點包括： 
    - 無縫監視 
    - 更好的基礎結構可見度 
    - 立即通知 
    - 自動解決 
    - 成本效益 

## <a name="integrated-azure-monitoring-architecture"></a>整合式 Azure 監視架構

下圖顯示 Azure VMware 解決方案 Vm 的整合式監視架構。

![整合式 Azure 監視架構](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>開始之前

如果您剛開始使用 Azure，或不熟悉先前提及的任何服務，請參閱下列文章：

- [自動化帳戶驗證總覽](../automation/automation-security-overview.md)
- [設計 Azure 監視器記錄部署](../azure-monitor/platform/design-logs-deployment.md) 和 [Azure 監視器](../azure-monitor/overview.md)
- Azure 資訊安全中心的[規劃](../security-center/security-center-planning-and-operations-guide.md)和[支援平臺](../security-center/security-center-os-coverage.md)
- [啟用適用於 VM 的 Azure 監視器概觀](../azure-monitor/insights/vminsights-enable-overview.md)
- [什麼是啟用 Azure Arc 的伺服器？](../azure-arc/servers/overview.md)[什麼是 Azure Arc 啟用的 Kubernetes？](../azure-arc/kubernetes/overview.md)
- [更新管理概觀](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>整合和部署 Azure 原生服務

### <a name="enable-azure-update-management"></a>啟用 Azure 更新管理

Azure 自動化中的 Azure 更新管理會在混合式環境中管理 Windows 和 Linux 電腦的作業系統更新。 它會監視修補合規性，並將修補偏差警示轉送到 Azure 監視器以進行補救。 Azure 更新管理必須連接到您的 Log Analytics 工作區，才能使用儲存的資料來評估 Vm 上的更新狀態。

1.  在您可以將 Log Analytics 新增至 Azure 更新管理之前，您必須先 [建立 Azure 自動化帳戶](../automation/automation-create-standalone-account.md)。 如果您想要使用範本來建立帳戶，請參閱[使用 Azure Resource Manager 範本建立自動化帳戶](../automation/quickstart-create-automation-account-template.md)。

2. **Log analytics 工作區** 使用 log analytics 代理程式或擴充功能來啟用記錄收集和效能計數器集合。 若要建立 Log Analytics 工作區，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 如果您想要的話，也可以透過 [CLI](../azure-monitor/learn/quick-create-workspace-cli.md)、 [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)或 [Azure Resource Manager 範本](../azure-monitor/samples/resource-manager-workspace.md)建立工作區。

3. 若要為您的 Vm 啟用 Azure 更新管理，請參閱 [從自動化帳戶啟用更新管理](../automation/update-management/enable-from-automation-account.md)。 在流程中，您會將 Log Analytics 工作區與您的自動化帳戶連結。 
 
4. 將 Vm 新增至 Azure 更新管理之後，您可以 [在 vm 上部署更新，並查看結果](../automation/update-management/deploy-updates.md)。 

### <a name="enable-azure-security-center"></a>啟用 Azure 資訊安全中心

Azure 資訊安全中心在雲端和內部部署的混合式工作負載之間提供先進的威脅防護。 它會評估 Azure VMware 解決方案 Vm 的弱點，並視需要提出警示。 這些安全性警示可以轉送到 Azure 監視器以進行解析。

Azure 資訊安全中心不需要部署。 如需詳細資訊，請參閱 [虛擬機器支援的功能](../security-center/security-center-services.md)清單。

1. 若要將 Azure VMware 解決方案 Vm 和非 Azure Vm 新增至安全中心，請參閱 [快速入門：設定 Azure 資訊安全中心](../security-center/security-center-get-started.md)。 

2. 從非 Azure 環境新增 Azure VMware 解決方案 Vm 或 Vm 之後，請在安全中心啟用 Azure Defender。 安全性中心將會評估 Vm 是否有潛在的安全性問題。 它也會在 [總覽] 索引標籤中提供建議。如需詳細資訊，請參閱 [Azure 資訊安全中心中的安全性建議](../security-center/security-center-recommendations.md)。

3. 您可以在 Azure 資訊安全中心中定義安全性原則。 如需有關設定安全性原則的詳細資訊，請參閱 [使用安全性原則](../security-center/tutorial-security-policy.md)。

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>將 Vm 上架到已啟用 Azure Arc 的伺服器

Azure Arc 將 Azure 管理延伸到任何基礎結構，包括 Azure VMware 解決方案、內部部署或其他雲端平臺。

- 請參閱 [大規模將混合式機器連線到 Azure](../azure-arc/servers/onboard-service-principal.md) ，以啟用多個 Windows 或 Linux vm 的 Azure Arc 啟用的伺服器。

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>使用已啟用 Arc 的 Kubernetes 上架混合式 Kubernetes 叢集

您可以使用已啟用 Azure Arc 的 Kubernetes，連接 Azure VMware 解決方案環境中裝載的 Kubernetes 叢集。 

- 請參閱 [建立已啟用 Azure Arc 的上架服務主體](../azure-arc/kubernetes/create-onboarding-service-principal.md)。

### <a name="deploy-the-log-analytics-agent"></a>部署 Log Analytics 代理程式

您可以透過 Log Analytics 代理程式監視 Azure VMware 解決方案 Vm (也稱為 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理程式) 。 您已建立 Log Analytics 工作區，同時啟用 Azure 自動化更新管理。

- 使用 [啟用 Azure Arc 的伺服器 VM 延伸模組支援](../azure-arc/servers/manage-vm-extensions.md)來部署 Log Analytics 代理程式。

### <a name="enable-azure-monitor"></a>啟用 Azure 監視器

Azure 監視器是一套完整的解決方案，可收集、分析及處理來自雲端和內部部署環境的遙測資料。 不需要部署。 使用 Azure 監視器，您可以監視客體作業系統效能，以及探索和對應 Azure VMware 解決方案或內部部署 Vm 的應用程式相依性。

- Azure 監視器可讓您收集來自不同來源的資料，以進行監視和分析。 如需詳細資訊，請參閱 [Azure 監視器的監視資料來源](../azure-monitor/platform/data-sources.md)。

- 收集不同類型的資料以供分析、視覺化和警示。 如需詳細資訊，請參閱 [Azure 監視器資料平臺](../azure-monitor/platform/data-platform.md)。

- 若要使用 Log Analytics 工作區設定 Azure 監視器，請參閱 [設定適用於 VM 的 Azure 監視器的 Log analytics 工作區](../azure-monitor/insights/vminsights-configure-workspace.md)。

- 您可以建立警示規則來識別環境中的問題，例如資源的高使用率、遺失的修補程式、磁碟空間不足，以及 Vm 的心跳。 您也可以將警示傳送至 IT 服務管理 (ITSM) 工具，以設定偵測到事件的自動回應。 警示偵測通知也可以透過電子郵件傳送。 若要建立這類規則，請參閱：
    - [使用 Azure 監視器建立、查看和管理計量警示](../azure-monitor/platform/alerts-metric.md)。
    - [使用 Azure 監視器建立、查看和記錄管理警示](../azure-monitor/platform/alerts-log.md)。
    - 設定自動化動作和通知的[動作規則](../azure-monitor/platform/alerts-action-rules.md)。
    - [使用 IT 服務管理連接器將 Azure 連接到 ITSM 工具](../azure-monitor/platform/itsmc-overview.md)。