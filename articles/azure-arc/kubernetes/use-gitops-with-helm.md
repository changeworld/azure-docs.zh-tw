---
title: '在啟用 Arc 的 Kubernetes 叢集上使用 GitOps 部署 Helm 圖表 (預覽) '
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 針對已啟用 Azure Arc 的叢集設定搭配使用 GitOps 與 Helm (預覽)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, 容器
ms.openlocfilehash: 44803338a27fc492f4dc896a0edb398b2ce486ea
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926122"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>在啟用 Arc 的 Kubernetes 叢集上使用 GitOps 部署 Helm 圖表 (預覽) 

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

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>概述如何搭配使用 GitOps 和 Helm 與已啟用 Azure Arc 的 Kubernetes

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
    └── vote-app.yaml
```

在 git 存放庫中，我們有兩個目錄，一個包含 Helm 圖，一個包含發行設定。在 `releases` 目錄中， `vote-app.yaml` 包含如下所示的 HelmRelease 設定：

```bash
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: vote-app
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-vote
  values:
    frontendServiceName: arc-k8s-demo-vote-front
```

Helm 版本設定包含下列欄位：

- `metadata.name` 是必要的，且必須遵循 Kubernetes 命名慣例
- `metadata.namespace` 是選擇性的，且會決定建立版本的位置
- `spec.releaseName` 是選擇性的，如果未提供，則版本名稱將為 $namespace-$name
- `spec.chart.path` 是包含圖表的目錄，指定相對於儲存機制根路徑
- `spec.values` 是來自圖表本身的使用者自訂預設參數值

在 HelmRelease spec.values 中指定的選項將會覆寫圖表來源的 values.yaml 中所指定選項。

您可以在官方 [Helm Operator 文件](https://docs.fluxcd.io/projects/helm-operator/en/stable/)中深入了解 HelmRelease

## <a name="create-a-configuration"></a>建立設定

使用適用於 `k8sconfiguration` 的 Azure CLI 延伸模組，即可將已連線的叢集連結至範例 Git 存放庫。 我們會將此設定命名為 `azure-voting-app`，並在 `arc-k8s-demo` 命名空間中部署 Flux 運算子。

```bash
az k8sconfiguration create --name azure-voting-app \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>組態參數

若要自訂設定的建立，[深入了解您可以使用的其他參數](./use-gitops-connected-cluster.md#additional-parameters)。

## <a name="validate-the-configuration"></a>驗證組態

使用 Azure CLI 驗證是否已成功建立 `sourceControlConfiguration`。

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

`sourceControlConfiguration`資源會以合規性狀態、訊息和調試資訊進行更新。

**輸出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-voting-app",
  "name": "azure-voting-app",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>驗證應用程式

執行下列命令，並 `localhost:3000` 在瀏覽器上流覽至，確認應用程式正在執行。

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo-vote-front 3000:80
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure 原則來控管叢集設定](./use-azure-policy.md)
