---
title: 如何為容器啟用 Azure 監視器 |微軟文檔
description: 本文介紹如何為容器啟用和配置 Azure 監視器，以便了解容器的性能以及已識別哪些與性能相關的問題。
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275304"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何為容器啟用 Azure 監視器

本文概述了可用於為容器設置 Azure 監視器的選項，以監視部署到 Kubernetes 環境並託管在以下位置的工作負載的性能：

- [Azure 庫伯奈斯服務](https://docs.microsoft.com/azure/aks/)（AKS）

- 使用[AKS 引擎](https://github.com/Azure/aks-engine)託管在 Azure 上的自管理的庫伯奈斯群集。

- 使用 AKS 引擎託管在[Azure 堆疊](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或本地的自管理的 Kubernetes 群集。

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

可以使用以下支援的方法為容器的 Azure 監視器啟用新的或多種 Kubernetes 的現有部署：

- 從 Azure 門戶、Azure PowerShell 或 Azure CLI

- 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您有下列項目：

- **日誌分析工作區。**

    容器的 Azure 監視器支援 Azure[產品中按區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)列出的區域中的日誌分析工作區。

    當您啟用監視新的 AKS 群集或讓載入體驗在 AKS 群集訂閱的預設資源組中創建預設工作區時，可以創建工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../learn/quick-create-workspace.md)中建立它。 有關用於預設工作區的受支援映射對的清單，請參閱[容器 Azure 監視器的區域映射](container-insights-region-mapping.md)。

- 您是**日誌分析參與者角色**的成員，以啟用容器監視。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。

- 您是 AKS 群集**[資源的擁有者角色](../../role-based-access-control/built-in-roles.md#owner)** 的成員。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 預設情況下不收集普羅米古斯指標。 [在配置代理](container-insights-prometheus-integration.md)收集它們之前，請務必查看 Prometheus[文檔](https://prometheus.io/)，以瞭解可以刮擦的內容和支援的方法。

## <a name="supported-configurations"></a>支援的設定

容器的 Azure 監視器正式支援以下操作。

- 環境：Azure 紅帽 OpenShift、本地庫伯內特和 Azure 堆疊上的 AKS 引擎。 有關詳細資訊，請參閱[Azure 堆疊上的 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- 庫伯內斯的版本和支援策略與[支援的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。 

## <a name="network-firewall-requirements"></a>網路防火牆需求

下表中的資訊列出了容器化代理與容器的 Azure 監視器通信所需的代理和防火牆配置資訊。 從代理到 Azure 監視器的所有網路流量都來自外站。

|代理程式資源|連接埠 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

下表中的資訊列出了 Azure 中國的代理和防火牆配置資訊。

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | 資料擷取 |
| *.oms.opinsights.azure.cn | 443 | OMS 入職 |
| *.blob.core.windows.net | 443 | 用於監視出站連接。 |
| microsoft.com | 80 | 用於網路連接。 僅當代理映射版本為 ciprod09262019 或更早時，才需要這樣做。 |
| dc.services.visualstudio.com | 443 | 用於使用 Azure 公共雲應用程式見解進行代理遙測。 |

下表中的資訊列出了 Azure 美國政府的代理和防火牆配置資訊。

|代理程式資源|連接埠 |描述 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 資料擷取 |
| *.oms.opinsights.azure.us | 443 | OMS 入職 |
| *.blob.core.windows.net | 443 | 用於監視出站連接。 |
| microsoft.com | 80 | 用於網路連接。 僅當代理映射版本為 ciprod09262019 或更早時，才需要這樣做。 |
| dc.services.visualstudio.com | 443 | 使用 Azure 公共雲應用程式見解進行代理遙測。 |

## <a name="components"></a>元件

監視性能的能力依賴于專為容器 Azure 監視器開發的 Linux 的容器化日誌分析代理。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 代理程式版本是 microsoft/oms:ciprod04202018 或更新版本，且會以下列格式的日期呈現：*mmddyyyy*。

>[!NOTE]
>使用對 AKS 的 Windows 伺服器支援的預覽版本，具有 Windows Server 節點的 AKS 群集沒有安裝代理來收集資料並轉發到 Azure 監視器。 相反，作為標準部署的一部分，在群集中自動部署的 Linux 節點會代表群集中的所有 Windows 節點收集資料並將其轉發到 Azure 監視器。  
>

發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。

>[!NOTE]
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。
>範本必須部署在叢集所在的資源群組。

通過使用下表中描述的以下方法之一，為容器啟用 Azure 監視器。

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 新的 AKS 庫伯內斯群集 | [使用 Azure CLI 創建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 您可以啟用監視使用 Azure CLI 創建的新 AKS 群集。 |
| | [使用 Terraform 創建 AKS 群集](container-insights-enable-new-cluster.md#enable-using-terraform)| 您可以使用開源工具 Terraform 啟用對創建新 AKS 群集的監視。 |
| | [使用 Azure 資源管理器範本創建 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 您可以啟用監視使用預配置的 Azure 資源管理器範本創建的新 OpenShift 群集。 |
| | [使用 Azure CLI 創建打開移位群集](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 您可以使用 Azure CLI 部署新的 OpenShift 群集時啟用監視。 |
| 現有 AKS 庫伯奈斯群集 | [使用 Azure CLI 為 AKS 群集啟用](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 您可以啟用監視已使用 Azure CLI 部署的 AKS 群集。 |
| |[使用 Terraform 為 AKS 群集啟用](container-insights-enable-existing-clusters.md#enable-using-terraform) | 您可以使用開源工具 Terraform 啟用對已部署的 AKS 群集的監視。 |
| | [從 Azure 監視器為 AKS 群集啟用](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以啟用監視從 Azure 監視器中的多群集頁面部署的一個或多個 AKS 群集。 |
| | [從 AKS 群集啟用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 可以直接從 Azure 門戶中的 AKS 群集啟用監視。 |
| | [使用 Azure 資源管理器範本為 AKS 群集啟用](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 您可以使用預配置的 Azure 資源管理器範本啟用 AKS 群集的監視。 |
| | [啟用混合庫伯奈斯群集](container-insights-hybrid-setup.md) | 您可以啟用監視託管在 Azure 堆疊中的 AKS 引擎，也可以監視本地託管的庫伯內特。 |
| | [使用 Azure 資源管理器範本啟用 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 您可以使用預先配置的 Azure 資源管理器範本啟用監視現有 OpenShift 群集。 |
| | [啟用 Azure 監視器的 OpenShift 群集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 您可以啟用監視從 Azure 監視器中的多群集頁面部署的一個或多個 OpenShift 群集。 |

## <a name="next-steps"></a>後續步驟

- 啟用監視後，可以開始分析託管在 Azure 庫伯奈斯服務 （AKS）、Azure 堆疊或其他環境中的庫伯奈斯群集的性能。 要瞭解如何將 Azure 監視器用於容器，請參閱[查看庫伯奈斯群集性能](container-insights-analyze.md)。
