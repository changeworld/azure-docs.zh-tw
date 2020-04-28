---
title: 如何啟用容器的 Azure 監視器 |Microsoft Docs
description: 本文說明如何啟用和設定容器的 Azure 監視器，讓您可以瞭解容器的執行方式，以及已識別出哪些效能相關問題。
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 284668dfe2aec809ce9a94e15f7c83cf0f2025b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185926"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何啟用容器的 Azure 監視器

本文概述適用于容器的設定 Azure 監視器的選項，以監視部署至 Kubernetes 環境並裝載于的工作負載效能：

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) （AKS）

- 使用[AKS 引擎](https://github.com/Azure/aks-engine)在 Azure 上託管的自我管理 Kubernetes 叢集。

- 使用 AKS 引擎在[Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或內部部署上託管的自我管理 Kubernetes 叢集。

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3.x 和4.x 版

- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4.x 版

您可以使用下列支援的方法，針對新的或 Kubernetes 的一或多個現有部署啟用容器的 Azure 監視器：

- 從 Azure 入口網站、Azure PowerShell 或 Azure CLI

- 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您有下列項目：

- **Log Analytics 工作區。**

    容器的 Azure 監視器支援[依區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)在 Azure 產品中列出的區域中的 Log Analytics 工作區。

    當您啟用新 AKS 叢集的監視，或讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區時，您可以建立工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../learn/quick-create-workspace.md)中建立它。 如需預設工作區所支援對應配對的清單，請參閱[容器 Azure 監視器的區域對應](container-insights-region-mapping.md)。

- 您是**Log Analytics 參與者角色**的成員，可啟用容器監視。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。

- 您是 AKS 叢集資源上 [**[擁有](../../role-based-access-control/built-in-roles.md#owner)** 者] 角色的成員。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 預設不會收集 Prometheus 計量。 在設定[代理程式](container-insights-prometheus-integration.md)來收集它們之前，請務必參閱 Prometheus[檔](https://prometheus.io/)，以瞭解可以剪輯的專案和支援的方法。

## <a name="supported-configurations"></a>支援的設定

以下已正式支援容器的 Azure 監視器。

- 環境： azure Red Hat OpenShift、Kubernetes 內部部署和 Azure 上的 AKS 引擎和 Azure Stack。 如需詳細資訊，請參閱[Azure Stack 上的 AKS Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- Kubernetes 和支援原則的版本與[支援的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。 

## <a name="network-firewall-requirements"></a>網路防火牆需求

下表中的資訊列出容器化代理程式與容器的 Azure 監視器進行通訊所需的 proxy 和防火牆設定資訊。 來自代理程式的所有網路流量都會輸出到 Azure 監視器。

|代理程式資源|連接埠 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

下表中的資訊列出 Azure 中國的 proxy 和防火牆設定資訊。

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | 資料擷取 |
| *. oms.opinsights.azure.cn | 443 | OMS 上線 |
| microsoft.com | 80 | 用於網路連接。 只有在代理程式映射版本為 ciprod09262019 或更早版本時，才需要此參數。 |
| dc.services.visualstudio.com | 443 | 適用于使用 Azure 公用雲端 Application Insights 的代理程式遙測。 |

下表中的資訊列出 Azure 美國政府的 proxy 和防火牆設定資訊。

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 資料擷取 |
| *.oms.opinsights.azure.us | 443 | OMS 上線 |
| microsoft.com | 80 | 用於網路連接。 只有在代理程式映射版本為 ciprod09262019 或更早版本時，才需要此參數。 |
| dc.services.visualstudio.com | 443 | 針對使用 Azure 公用雲端 Application Insights 的代理程式遙測。 |

## <a name="components"></a>元件

您監視效能的能力取決於專為容器的 Azure 監視器而開發之適用于 Linux 的容器化 Log Analytics 代理程式。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 代理程式版本是 microsoft/oms:ciprod04202018 或更新版本，且會以下列格式的日期呈現：*mmddyyyy*。

>[!NOTE]
>有了 AKS 的 Windows Server 支援預覽版本，具有 Windows Server 節點的 AKS 叢集並不會安裝代理程式來收集資料並轉送到 Azure 監視器。 相反地，在叢集中自動部署為標準部署一部分的 Linux 節點會收集資料，並將其轉送到叢集中的所有 Windows 節點 Azure 監視器。  
>

發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。

>[!NOTE]
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。
>範本必須部署在叢集所在的資源群組。

您可以使用下表所述的下列其中一種方法來啟用容器的 Azure 監視器。

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 新增 AKS Kubernetes 叢集 | [使用 Azure CLI 建立 AKS 叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 您可以啟用使用 Azure CLI 建立的新 AKS 叢集的監視。 |
| | [使用 Terraform 建立 AKS 叢集](container-insights-enable-new-cluster.md#enable-using-terraform)| 您可以使用開放原始碼工具 Terraform，啟用您所建立之新 AKS 叢集的監視。 |
| | [使用 Azure Resource Manager 範本建立 OpenShift 叢集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 您可以啟用以預先設定的 Azure Resource Manager 範本來建立新 OpenShift 叢集的監視。 |
| | [使用 Azure CLI 建立 OpenShift 叢集](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 使用 Azure CLI 部署新的 OpenShift 叢集時，您可以啟用監視。 |
| 現有的 AKS Kubernetes 叢集 | [使用 Azure CLI 啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 您可以使用 Azure CLI 來啟用已部署之 AKS 叢集的監視。 |
| |[使用 Terraform 啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-using-terraform) | 您可以使用開放原始碼工具 Terraform，啟用已部署之 AKS 叢集的監視。 |
| | [從 Azure 監視器啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以從 Azure 監視器中的多個叢集頁面，啟用已部署的一或多個 AKS 叢集的監視。 |
| | [從 AKS 叢集啟用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 您可以直接從 Azure 入口網站中的 AKS 叢集啟用監視。 |
| | [使用 Azure Resource Manager 範本啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 您可以使用預先設定的 Azure Resource Manager 範本來啟用 AKS 叢集的監視。 |
| | [針對混合式 Kubernetes 叢集啟用](container-insights-hybrid-setup.md) | 您可以針對裝載于內部部署的 AKS 引擎，啟用監視 Azure Stack 或主控的 Kubernetes。 |
| | [使用 Azure Resource Manager 範本啟用 OpenShift 叢集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 您可以使用預先設定的 Azure Resource Manager 範本來啟用現有 OpenShift 叢集的監視。 |
| | [從 Azure 監視器啟用 OpenShift 叢集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 您可以從 Azure 監視器中的多個叢集頁面，啟用已部署的一或多個 OpenShift 叢集的監視。 |

## <a name="next-steps"></a>後續步驟

- 啟用監視功能之後，您就可以開始分析 Azure Kubernetes Service （AKS）、Azure Stack 或其他環境上裝載的 Kubernetes 叢集效能。 若要瞭解如何使用容器的 Azure 監視器，請參閱[View Kubernetes cluster performance](container-insights-analyze.md)。
