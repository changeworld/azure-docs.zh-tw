---
title: 啟用容器的 Azure 監視器 |Microsoft Docs
description: 本文說明如何啟用及設定容器的 Azure 監視器，讓您可以瞭解容器的執行方式，以及已識別出哪些效能相關的問題。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1cb393faded714593ce635669f585d5979ee69b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320301"
---
# <a name="enable-azure-monitor-for-containers"></a>啟用容器的 Azure 監視器

本文概述可用來設定容器 Azure 監視器的選項，以監視部署至 Kubernetes 環境並裝載于上的工作負載效能：

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3.x 和4.x 版  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4.x 版  
- [啟用 Arc 的 Kubernetes](../../azure-arc/kubernetes/overview.md)叢集

您也可以監視部署至裝載于之自我管理 Kubernetes 叢集的工作負載效能：
- Azure，使用 [AKS 引擎](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 或內部部署，方法是使用 AKS 引擎。

您可以使用下列任何一種支援的方法，為新部署或 Kubernetes 的一或多個現有部署啟用容器的 Azure 監視器：

- Azure 入口網站
- Azure PowerShell
- Azure CLI
- [Terraform 和 AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您已符合下列需求：

- 您有 Log Analytics 工作區。

   適用于容器的 Azure 監視器支援 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)中所列之區域中的 Log Analytics 工作區。

   當您啟用新 AKS 叢集的監視功能時，您可以建立工作區，也可以讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區。 
   
   如果您選擇自行建立工作區，您可以透過下列方式建立工作區： 
   - [Azure Resource Manager](../platform/template-workspace-configuration.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure 入口網站](../learn/quick-create-workspace.md) 
   
   如需預設工作區所支援的對應組清單，請參閱 [容器 Azure 監視器的區域對應](container-insights-region-mapping.md)。

- 您是用來啟用容器監視的 *Log Analytics 參與者* 群組的成員。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。

- 您是 AKS 叢集資源上 [*擁有* 者群組](../../role-based-access-control/built-in-roles.md#owner) 的成員。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- 若要查看監視資料，您必須在 Log Analytics 工作區中擁有 [*Log analytics 讀者*](../platform/manage-access.md#manage-access-using-azure-permissions) 角色，並使用容器的 Azure 監視器設定。

- 依預設不會收集 Prometheus 計量。 在您 [設定代理程式](container-insights-prometheus-integration.md) 來收集計量之前，請務必先參閱 [Prometheus 檔](https://prometheus.io/) ，以瞭解可以剪輯的資料，以及支援的方法。

## <a name="supported-configurations"></a>支援的設定

適用于容器的 Azure 監視器正式支援下列設定：

- 環境： Azure Red Hat OpenShift、Kubernetes 內部部署，以及 Azure 和 Azure Stack 上的 AKS 引擎。 如需詳細資訊，請參閱 [Azure Stack 上的 AKS 引擎](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- Kubernetes 和支援原則的版本與 [Azure Kubernetes Service (AKS) 所支援 ](../../aks/supported-kubernetes-versions.md)的版本相同。 

## <a name="network-firewall-requirements"></a>網路防火牆需求

下表列出容器化代理程式與容器的 Azure 監視器進行通訊所需的 proxy 和防火牆設定資訊。 來自代理程式的所有網路流量都會輸出至 Azure 監視器。

|代理程式資源|連接埠 |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

下表列出 Azure 中國世紀的 proxy 和防火牆設定資訊：

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | 資料擷取 |
| `*.oms.opinsights.azure.cn` | 443 | OMS 上線 |
| `dc.services.visualstudio.com` | 443 | 針對使用 Azure 公用雲端的代理程式遙測 Application Insights |

下表列出 Azure 美國政府的 proxy 和防火牆設定資訊：

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | 資料擷取 |
| `*.oms.opinsights.azure.us` | 443 | OMS 上線 |
| `dc.services.visualstudio.com` | 443 | 針對使用 Azure 公用雲端的代理程式遙測 Application Insights |

## <a name="components"></a>元件

您監視效能的能力依賴適用于 Linux 的容器化 Log Analytics 代理程式，專為容器的 Azure 監視器而開發。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 

代理程式版本是 microsoft/oms： ciprod04202018 或更新版本，且會以下列格式的日期表示： *mmddyyyy*。

>[!NOTE]
>由於 Windows Server 的 AKS 支援已正式運作，因此具有 Windows Server 節點的 AKS 叢集會在每個個別的 Windows server 節點上安裝預覽代理程式做為 daemonset pod，以收集記錄並將記錄轉送至 Log Analytics。 針對效能計量，在叢集中自動部署于叢集中作為標準部署一部分的 Linux 節點會收集並轉送資料，以代表叢集中的所有 Windows 節點 Azure 監視器。

當發行新版的代理程式時，它會自動在裝載于 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上進行升級。 若要追蹤已發行的版本，請參閱 [代理程式發行宣告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。

> [!NOTE]
> 如果您已部署 AKS 叢集，您已使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如本文稍後所示。 您無法使用 `kubectl` 升級、刪除、重新部署或部署代理程式。
>
> 範本必須部署在叢集所在的資源群組。

若要啟用容器的 Azure 監視器，請使用下表所述的其中一種方法：

| 部署狀態 | 方法 | 說明 |
|------------------|--------|-------------|
| 新的 Kubernetes 叢集 | [使用 Azure CLI 建立 AKS 叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 您可以針對使用 Azure CLI 所建立的新 AKS 叢集啟用監視。 |
| | [使用 Terraform 建立 AKS 叢集](container-insights-enable-new-cluster.md#enable-using-terraform)| 您可以使用開放原始碼工具 Terraform，針對您所建立的新 AKS 叢集啟用監視。 |
| | [使用 Azure Resource Manager 範本建立 OpenShift 叢集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 您可以使用預先設定的 Azure Resource Manager 範本，為您建立的新 OpenShift 叢集啟用監視。 |
| | [使用 Azure CLI 建立 OpenShift 叢集](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 當您使用 Azure CLI 部署新的 OpenShift 叢集時，可以啟用監視。 |
| 現有的 Kubernetes 叢集 | [使用 Azure CLI 啟用 AKS 叢集的監視](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 您可以針對已使用 Azure CLI 部署的 AKS 叢集啟用監視。 |
| |[使用 Terraform 啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-using-terraform) | 您可以針對已使用開放原始碼工具 Terraform 部署的 AKS 叢集啟用監視。 |
| | [從 Azure 監視器啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以針對已從 Azure 監視器中的多叢集頁面部署的一或多個 AKS 叢集啟用監視。 |
| | [從 AKS 叢集啟用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 您可以直接從 Azure 入口網站中的 AKS 叢集啟用監視。 |
| | [使用 Azure Resource Manager 範本啟用 AKS 叢集](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 您可以使用預先設定的 Azure Resource Manager 範本來啟用 AKS 叢集的監視。 |
| | [為混合式 Kubernetes 叢集啟用](container-insights-hybrid-setup.md) | 您可以針對裝載于 Azure Stack 上的 AKS 引擎，或裝載于內部部署的 Kubernetes 叢集啟用監視。 |
| | [啟用啟用 Arc 的 Kubernetes](container-insights-enable-arc-enabled-clusters.md)叢集。 | 您可以針對裝載于 Azure 外部且使用 Azure Arc 啟用的 Kubernetes 叢集啟用監視。 |
| | [使用 Azure Resource Manager 範本啟用 OpenShift 叢集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 您可以使用預先設定的 Azure Resource Manager 範本，為現有的 OpenShift 叢集啟用監視。 |
| | [從 Azure 監視器啟用 OpenShift 叢集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 您可以針對已從 Azure 監視器中的 [multicluster] 頁面部署的一或多個 OpenShift 叢集啟用監視。 |

## <a name="next-steps"></a>接下來的步驟

現在您已啟用監視，您可以開始分析裝載于 Azure Kubernetes Service (AKS) 、Azure Stack 或其他環境上的 Kubernetes 叢集效能。 若要瞭解如何使用容器 Azure 監視器，請參閱 [View Kubernetes cluster performance](container-insights-analyze.md)。

