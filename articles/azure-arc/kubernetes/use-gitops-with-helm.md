---
title: 針對已啟用 Azure Arc 的叢集設定搭配使用 GitOps 與 Helm (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 針對已啟用 Azure Arc 的叢集設定搭配使用 GitOps 與 Helm (預覽)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, 容器
ms.openlocfilehash: 677c5f2b27794ebea9d38e470b5e1a5ba12bff7e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857215"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>針對已啟用 Azure Arc 的叢集設定搭配使用 GitOps 與 Helm (預覽)

Helm 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 APT 和 Yum 等 Linux 套件管理員，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文示範如何透過已啟用 Azure Arc 的 Kubernetes 來設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

本文假設您具有現有已啟用 Azure Arc 的 Kubernetes 已連線叢集。 如果您需要已連線的叢集，請參閱[連線叢集快速入門](./connect-cluster.md)。

讓我們先設定環境變數，以便在整個教學課程中使用。 您將需要已連線叢集的資源群組名稱和叢集名稱。

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>確認您的叢集已啟用 Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

輸出：
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>使用 Helm 搭配已啟用 Azure Arc 的 Kubernetes 總覽

 Helm 運算子提供 Flux 的延伸模組，可將 Helm 圖表版本自動化。 圖表版本是透過名為 HelmRelease 的 Kubernetes 自訂資源進行描述。 Flux 會將這些資源從 git 同步處理到叢集，而 Helm 運算子可確保如資源中所指定來釋放 Helm 圖表。

 以下是我們將在本教學課程中使用的範例 git 存放庫結構：

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

在 git 存放庫中，我們有兩個目錄，一個包含 Helm 圖表，一個包含版本設定。在 `releases/prod` 目錄中，`azure-vote-app.yaml` 包含如下所示的 HelmRelease 設定：

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Helm 版本設定包含下列欄位：

- `metadata.name` 是必要的，且必須遵循 Kubernetes 命名慣例
- `metadata.namespace` 是選擇性的，且會決定建立版本的位置
- `spec.releaseName` 是選擇性的，如果未提供，則版本名稱將為 $namespace-$name
- `spec.chart.path` 是包含圖表的目錄，指定相對於儲存機制根路徑
- `spec.values` 是來自圖表本身的使用者自訂預設參數值

在 HelmRelease spec.values 中指定的選項將會覆寫圖表來源的 values.yaml 中所指定選項。

您可以在官方 [Helm Operator 文件](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)中深入了解 HelmRelease

## <a name="create-a-configuration"></a>建立設定

使用適用於 `k8sconfiguration` 的 Azure CLI 延伸模組，即可將已連線的叢集連結至範例 Git 存放庫。 我們會將此設定命名為 `azure-voting-app`，並在 `prod` 命名空間中部署 Flux 運算子。

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>組態參數

若要自訂設定的建立，[深入了解您可以使用的其他參數](./use-gitops-connected-cluster.md#additional-parameters)。


## <a name="validate-the-configuration"></a>驗證組態

使用 Azure CLI 驗證是否已成功建立 `sourceControlConfiguration`。

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

請注意，`sourceControlConfiguration` 資源會利用合規性狀態、訊息和偵錯資訊進行更新。

**輸出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>驗證應用程式

我們現在會藉由取得服務 IP 來檢查並確認應用程式是否已啟動且正在執行。

```bash
kubectl get svc/azure-vote-front -n prod
```

**輸出：**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

從上述輸出尋找外部 IP 位址，並在瀏覽器中加以開啟。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 原則來治理叢集設定](./use-azure-policy.md)
