---
title: 連線已啟用 Azure Arc 的 Kubernetes 叢集 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 將已啟用 Azure Arc 的 Kubernetes 叢集與 Azure Arc 連線
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860540"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>連線已啟用 Azure Arc 的 Kubernetes 叢集 (預覽)

無法將 Kubernetes 叢集連線到 Azure Arc。 

## <a name="before-you-begin"></a>開始之前

確認您已備妥下列需求：

* 已啟動且正在執行的 Kubernetes 叢集
* 您將需要使用 kubeconfig 和叢集管理員存取權。 
* 搭配 `az login` 和 `az connectedk8s connect` 命令使用的使用者或服務主體，必須具有「Microsoft.Kubernetes/connectedclusters」資源類型的「讀取」和「寫入」權限。 具有這些權限的「適用於 Kubernetes 的 Azure Arc 上線」角色可用於使用者上的角色指派，或是與 Azure CLI 搭配使用的服務主體以用於上線。
* 最新版的 connectedk8s 和 k8sconfiguration 延伸模組

## <a name="supported-regions"></a>支援區域

* 美國東部
* 西歐

## <a name="network-requirements"></a>網路需求

Azure Arc 代理程式需要下列通訊協定/連接埠/輸出 URL 才能運作。

* 連接埠 443 上的 TCP --> `https://:443`
* 連接埠 9418 上的 TCP --> `git://:9418`

| 端點 (DNS)                                                                                               | 描述                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | 代理程式連線到 Azure 並註冊叢集所需                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | 代理程式的資料平面端點，用來推送狀態和擷取設定資訊                                      |
| `https://docker.io`                                                                                            | 提取容器映像所需                                                                                         |
| `https://github.com`，git://github.com                                                                         | 範例 GitOps 存放庫裝載於 GitHub 上。 設定代理程式需要連線到您指定的任何 git 端點。 |
| `https://login.microsoftonline.com`                                                                            | 擷取和更新 Azure Resource Manager 權杖所需                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | 提取 Azure Arc 代理程式的容器映像時所需                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>為已啟用 Azure Arc 的 Kubernetes 註冊兩個提供者：

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

註冊是非同步程序。 註冊可能約需要 10 分鐘。 您可以使用下列命令來監視註冊程序：

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>安裝 Azure CLI 延伸模組

安裝 `connectedk8s` 延伸模組，以協助您將 Kubernetes 叢集連線到 Azure：

```console
az extension add --name connectedk8s
```

安裝 `k8sconfiguration` 延伸模組：

```console
az extension add --name k8sconfiguration
```

執行下列命令將延伸模組更新為最新版本。

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>建立資源群組

使用資源群組來儲存叢集的中繼資料。

首先，建立資源群組來保存已連線的叢集資源。

```console
az group create --name AzureArcTest -l EastUS -o table
```

**輸出：**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>連線叢集

接下來，我們會將 Kubernetes 叢集連線到 Azure。 `az connectedk8s connect` 的工作流程如下︰

1. 確認 Kubernetes 叢集的連線：透過 `KUBECONFIG`、`~/.kube/config` 或 `--kube-config`
1. 使用 Helm 3 將適用於 Kubernetes 的 Azure Arc 代理程式部署到 `azure-arc` 命名空間

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**輸出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>驗證已連線的叢集

列出已連線的叢集：

```console
az connectedk8s list -g AzureArcTest -o table
```

**輸出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

啟用 Azure Arc 的 Kubernetes 會在 `azure-arc` 命名空間內部署幾個運算子。 您可以在這裡檢視這些部署和 Pod：

```console
kubectl -n azure-arc get deployments,pods
```

**輸出：**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>適用於 Kubernetes 的 Azure Arc 代理程式

啟用 Azure Arc 的 Kubernetes 是由幾個代理程式 (運算子) 所組成，其會在您部署到 `azure-arc` 命名空間的叢集中執行。

* `deployment.apps/config-agent`：監看已連線的叢集，以了解叢集上套用的原始檔控制設定資源和更新合規性狀態
* `deployment.apps/controller-manager`：是運算子的運算子，且可協調 Azure Arc 元件之間的互動
* `deployment.apps/metrics-agent`：收集其他 Arc 代理程式的計量，以確保這些代理程式呈現最佳效能
* `deployment.apps/cluster-metadata-operator`：收集叢集中繼資料 - 叢集版本、節點計數和 Arc 代理程式版本
* `deployment.apps/resource-sync-agent`：將上述叢集中繼資料同步處理至 Azure
* `deployment.apps/clusteridentityoperator`：維護其他代理程式用來與 Azure 通訊的受控服務識別 (MSI) 憑證
* `deployment.apps/flux-logs-agent`：從部署為原始檔控制設定一部分的 flux 運算子收集記錄

## <a name="delete-a-connected-cluster"></a>刪除已連線的叢集

您可以使用 Azure CLI 或 Azure 入口網站來刪除 `Microsoft.Kubernetes/connectedcluster` 資源。

Azure CLI 命令 `az connectedk8s delete` 會移除 Azure 中的 `Microsoft.Kubernetes/connectedCluster` 資源。 Azure CLI 會刪除 Azure 中任何相關聯的 `sourcecontrolconfiguration` 資源。 Azure CLI 會使用 helm uninstall 來移除叢集中的代理程式。

Azure 入口網站會刪除 Azure 中的 `Microsoft.Kubernetes/connectedcluster` 資源，並刪除 Azure 中任何相關聯的 `sourcecontrolconfiguration` 資源。

若要移除叢集中的代理程式，您必須執行 `az connectedk8s delete` 或 `helm uninstall azurearcfork8s`。

## <a name="next-steps"></a>後續步驟

* [在連線的叢集中使用 GitOps](./use-gitops-connected-cluster.md)
* [使用 Azure 原則來治理叢集設定](./use-azure-policy.md)
