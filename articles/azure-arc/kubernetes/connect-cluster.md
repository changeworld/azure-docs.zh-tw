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
ms.custom: references_regions
ms.openlocfilehash: 761263a4cb8c83475142c2afcc39695bb84d46cd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080485"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>連線已啟用 Azure Arc 的 Kubernetes 叢集 (預覽)

本檔涵蓋將任何雲端原生運算基礎連接 (由 CNCF) 認證的 Kubernetes 叢集的程式，例如 Azure 上的 AKS 引擎、Azure Stack Hub 上的 AKS engine、GKE、EKS 和 VMware vSphere 叢集到 Azure Arc。

## <a name="before-you-begin"></a>開始之前

確認您已備妥下列需求：

* 已啟動且正在執行的 Kubernetes 叢集。 如果您沒有現有的 Kubernetes 叢集，您可以使用下列其中一個指南來建立測試叢集：
  * 使用 Docker 中的 Kubernetes 建立 Kubernetes 叢集[ (種類) ](https://kind.sigs.k8s.io/)
  * 使用適用于[Mac](https://docs.docker.com/docker-for-mac/#kubernetes)或[Windows](https://docs.docker.com/docker-for-windows/#kubernetes)的 Docker 建立 Kubernetes 叢集
* 您需要 kubeconfig 檔案，才能存取叢集上的叢集和叢集系統管理員角色，以部署已啟用 Arc 的 Kubernetes 代理程式。
* 搭配 `az login` 和 `az connectedk8s connect` 命令使用的使用者或服務主體，必須具有「Microsoft.Kubernetes/connectedclusters」資源類型的「讀取」和「寫入」權限。 「Kubernetes 叢集-Azure Arc 上架」角色具有這些許可權，可用於使用者或服務主體上的角色指派。
* 使用 connectedk8s 擴充功能讓叢集上線時，需要 Helm 3。 [安裝最新版本的 Helm 3](https://helm.sh/docs/intro/install)以符合這項需求。
* 安裝已啟用 Azure Arc 的 Kubernetes CLI 擴充功能需要 Azure CLI 版本 2.3 +。 [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或更新為最新版本，以確保您有 Azure CLI 版本 2.3 +。
* 安裝啟用 Arc 的 Kubernetes CLI 延伸模組：
  
  安裝 `connectedk8s` 延伸模組，以協助您將 Kubernetes 叢集連線到 Azure：
  
  ```console
  az extension add --name connectedk8s
  ```
  
  安裝 `k8sconfiguration` 延伸模組：
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  如果您稍後想要更新這些延伸模組，請執行下列命令：
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

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

az provider register --namespace Microsoft.KubernetesConfiguration
```

註冊是非同步程序。 註冊可能約需要 10 分鐘。 您可以使用下列命令來監視註冊程序：

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
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

您也可以在[Azure 入口網站](https://portal.azure.com/)上查看此資源。 在瀏覽器中開啟入口網站後，根據先前在命令中使用的資源名稱和資源組名輸入，流覽至資源群組和已啟用 Azure Arc 的 Kubernetes 資源 `az connectedk8s connect` 。

> [!NOTE]
> 上線叢集之後，大約需要5到10分鐘的時間，叢集中繼資料 (叢集版本、代理程式版本、節點數目) 在 Azure 入口網站的 Azure Arc 已啟用 Kubernetes 資源的 [總覽] 頁面上呈現。

## <a name="connect-using-an-outbound-proxy-server"></a>使用輸出 proxy 伺服器連接

如果您的叢集位於輸出 proxy 伺服器後方，Azure CLI 和啟用 Arc 的 Kubernetes 代理程式需要透過輸出 proxy 伺服器來路由傳送要求。 下列設定有助於達成該目標：

1. 執行下列命令，檢查 `connectedk8s` 安裝在您電腦上的延伸模組版本：

    ```bash
    az -v
    ```

    您需要 `connectedk8s` 擴充功能版本 >= 0.2.3，才能使用輸出 proxy 來設定代理程式。 如果您的電腦上有版本 < 0.2.3，請遵循[更新步驟](#before-you-begin)，在您的電腦上取得最新版本的延伸模組。

2. 設定 Azure CLI 所需的環境變數：

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

3. 使用指定的 proxy 參數執行 connect 命令：

    ```bash
    az connectedk8s connect -n <cluster-name> -g <resource-group> \
    --proxy-https https://<proxy-server-ip-address>:<port> \
    --proxy-http http://<proxy-server-ip-address>:<port> \
    --proxy-skip-range <excludedIP>,<excludedCIDR>
    ```

> [!NOTE]
> 1. 指定 excludedCIDR 的 [--proxy-略過範圍] 非常重要，可確保代理程式的叢集中通訊不會中斷。
> 2. 上述 proxy 規格目前僅適用于 Arc 代理程式，不適用於 sourceControlConfiguration 中所使用的 flux pod。 已啟用 Arc 的 Kubernetes 小組正積極處理這項功能，而且很快就會提供。

## <a name="azure-arc-agents-for-kubernetes"></a>適用於 Kubernetes 的 Azure Arc 代理程式

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

啟用 Azure Arc 的 Kubernetes 是由幾個代理程式 (運算子) 所組成，其會在您部署到 `azure-arc` 命名空間的叢集中執行。

* `deployment.apps/config-agent`：監看已連線的叢集，以了解叢集上套用的原始檔控制設定資源和更新合規性狀態
* `deployment.apps/controller-manager`：是運算子的運算子，且可協調 Azure Arc 元件之間的互動
* `deployment.apps/metrics-agent`：收集其他 Arc 代理程式的計量，以確保這些代理程式呈現最佳效能
* `deployment.apps/cluster-metadata-operator`：收集叢集中繼資料-叢集版本、節點計數和 Azure Arc 代理程式版本
* `deployment.apps/resource-sync-agent`：將上述叢集中繼資料同步處理至 Azure
* `deployment.apps/clusteridentityoperator`：已啟用 Azure Arc 的 Kubernetes 目前支援系統指派的身分識別。 clusteridentityoperator 會維護受控服務識別 (MSI) 憑證，供其他代理程式用來與 Azure 進行通訊。
* `deployment.apps/flux-logs-agent`：從部署為原始檔控制設定一部分的 flux 運算子收集記錄

## <a name="delete-a-connected-cluster"></a>刪除已連線的叢集

您可以使用 Azure CLI 或 Azure 入口網站來刪除 `Microsoft.Kubernetes/connectedcluster` 資源。


* **使用 Azure CLI 刪除**：下列 Azure CLI 命令可以用來起始刪除已啟用 Azure Arc 的 Kubernetes 資源。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  此命令會移除 `Microsoft.Kubernetes/connectedCluster` 資源和 Azure 中任何相關聯的 `sourcecontrolconfiguration` 資源。 Azure CLI 也會使用 helm 卸載來移除叢集上執行的代理程式。

* **在 Azure 入口網站上刪除**：刪除 Azure 入口網站上已啟用 Azure Arc 的 Kubernetes 資源 `Microsoft.Kubernetes/connectedcluster` 會刪除資源和 Azure 中任何相關聯的 `sourcecontrolconfiguration` 資源，但不會刪除在叢集上執行的代理程式。 若要刪除在叢集上執行的代理程式，請執行下列命令。

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>後續步驟

* [在連線的叢集中使用 GitOps](./use-gitops-connected-cluster.md)
* [使用 Azure 原則來治理叢集設定](./use-azure-policy.md)
