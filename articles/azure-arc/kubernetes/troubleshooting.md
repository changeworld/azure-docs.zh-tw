---
title: 針對已啟用 Azure Arc 的 Kubernetes 常見問題進行疑難排解 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 針對已啟用 Arc 的 Kubernetes 叢集常見問題進行疑難排解。
keywords: Kubernetes, Arc, Azure, 容器
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725579"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>已啟用 Azure Arc 的 Kubernetes 疑難排解 (預覽)

本文件提供一些常見的疑難排解案例，其中包含連線能力、許可權和代理程式。

## <a name="general-troubleshooting"></a>一般疑難排解

### <a name="azure-cli-set-up"></a>Azure CLI 設定
使用 az connectedk8s 或 az k8sconfiguration CLI 命令之前，請確定 az 已設定為針對正確的 Azure 訂用帳戶運作。

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>azure-arc 代理程式
所有已啟用 Azure Arc 的 Kubernetes 代理程式都會部署為 `azure-arc` 命名空間中的 Pod。 在正常作業下，所有 Pod 都應該在執行中，並通過健康狀態檢查。

首先，請確認 Azure Arc helm 版本：

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

如果找不到 Helm 版本或其遺失，請嘗試將叢集重新上線。

如果 Helm 版本存在，且 `STATUS: deployed` 使用 `kubectl` 來判斷代理程式的狀態：

```console
$ kubectl -n azure-arc get deployments,pods
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

所有 Pod 應會將 `STATUS` 顯示為 `Running`，而 `READY` 應為 `3/3` 或 `2/2`。 擷取記錄並描述傳回 `Error` 或 `CrashLoopBackOff` 的 Pod。

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>無法將 Kubernetes 叢集連線到 Azure

將叢集連線到 Azure 需要存取 Azure 訂用帳戶，以及對目標叢集的 `cluster-admin` 存取權。 如果無法連線到叢集，或沒有足夠的權限，上線將會失敗。

### <a name="insufficient-cluster-permissions"></a>叢集權限不足

如果提供的 kubeconfig 檔案沒有足夠權限來安裝 Azure Arc 代理程式，則 Azure CLI 命令會在嘗試呼叫 Kubernetes API 時傳回錯誤。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

叢集擁有者應該使用具有叢集系統管理員權限的 Kubernetes 使用者。

### <a name="installation-timeouts"></a>安裝逾時

Azure Arc 代理程式安裝需要在目標叢集上執行一組容器。 如果叢集是透過慢速網際網路連線執行，則容器映像提取可能會比 Azure CLI 逾時更久。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>設定管理

### <a name="general"></a>一般
若要協助對原始檔控制設定的問題進行疑難排解，請使用 --debug 參數來執行 az 命令。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>建立原始檔控制設定
Microsoft.Kubernetes/connectedCluster 資源上的參與者角色為必要，且足以建立 Microsoft.KubernetesConfiguration/sourceControlConfiguration 資源。

### <a name="configuration-remains-pending"></a>設定維持為 `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
