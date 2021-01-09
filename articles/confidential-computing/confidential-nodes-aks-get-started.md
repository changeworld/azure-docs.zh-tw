---
title: 快速入門：使用 Azure CLI 部署具有機密運算節點的 Azure Kubernetes Service (AKS) 叢集
description: 了解如何使用 Azure CLI 建立具有機密節點的 AKS 叢集，以及部署 Hello World 應用程式。
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: amgowda
ms.openlocfilehash: 92b4cd58b496602b479a24bab81a1d9322e732b0
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760634"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>快速入門：使用 Azure CLI 部署具有機密運算節點 (DCsv2) 的 Azure Kubernetes Service (AKS) 叢集 (預覽)

本快速入門適用於想要快速建立 AKS 叢集並部署應用程式，以使用 Azure 中的受控 Kubernetes 服務來監視應用程式的開發人員或叢集操作員。

## <a name="overview"></a>概觀

在本快速入門中，您將了解如何使用 Azure CLI 部署具有機密運算節點的 Azure Kubernetes Service (AKS) 叢集，並在記憶體保護區中執行 Hello World 應用程式。 AKS 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 請在[此處](../aks/intro-kubernetes.md)深入了解 AKS。

> [!NOTE]
> 機密運算 DCsv2 VM 採用特殊化硬體，而該硬體受限於較高的定價和區域可用性。 如需詳細資訊，請參閱[可用 SKU 和支援區域](virtual-machine-solutions.md)的虛擬機器頁面。

> DCsv2 會利用 Azure 上的第 2 代虛擬機器，此第 2 代 VM 是 AKS 的預覽功能。 

### <a name="deployment-pre-requisites"></a>部署必要條件
部署指示會假設您：

1. 具有有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
1. 在您的部署機器上安裝並設定 Azure CLI 2.0.64 版或更新版本 (執行 `az --version` 可尋找版本)。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
1. [aks-preview 擴充功能](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) (至少 0.4.62 版) 
1. VM 核心配額可用性。 您的訂用帳戶中至少要有六個 **DC<x>s-v2** 核心可供使用。 根據預設，每個 Azure 訂用帳戶的機密運算 VM 核心配額為 8 個核心。 如果您預計要佈建需要超過 8 個核心的叢集，請依照[這些](../azure-portal/supportability/per-vm-quota-requests.md)指示提出配額增加票證

### <a name="confidential-computing-node-features-dcxs-v2"></a>機密運算節點功能 (DC<x>s-v2)

1. 僅支援 Linux 容器的 Linux 背景工作節點
1. 具有 Ubuntu 18.04 虛擬機器節點的第 2 代 VM
1. 具有加密頁面快取記憶體 (EPC) 的 Intel SGX 型 CPU。 請在[這裡](./faq.md)閱讀更多資訊
1. 支援的 Kubernetes 版本 1.16+
1. AKS 節點上預先安裝了 Intel SGX DCAP 驅動程式。 請在[這裡](./faq.md)閱讀更多資訊
1. 支援在預覽期間以入口網站為基礎的佈建公佈的正式發行 CLI。


## <a name="installing-the-cli-pre-requisites"></a>安裝 CLI 必要條件

若要安裝 aks-preview 0.4.62 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
若要更新 aks-preview CLI 擴充功能，請使用下列 Azure CLI 命令：

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="generation-2-vms-feature-registration-on-azure"></a>Azure 上第 2 代 VM 的功能註冊
在 Azure 訂用帳戶上註冊 Gen2VMPreview。 這項功能可讓您將第 2 代虛擬機器佈建為 AKS 節點集區：

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 'az feature list' 命令檢查註冊狀態。 每個訂用帳戶只需註冊此功能一次。 如果先前已註冊，您可以略過上述步驟：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
當狀態顯示為已註冊時，請使用 'az provider register' 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Azure 上的 Azure 機密運算功能註冊 (選用但建議使用)
在 Azure 訂用帳戶上註冊 AKS-ConfidentialComputinAddon。 這項功能會新增兩個精靈集，詳細資料在[這裡](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon)：
1. SGX 裝置驅動程式外掛程式
2. SGX 證明報價協助程式

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 'az feature list' 命令檢查註冊狀態。 每個訂用帳戶只需註冊此功能一次。 如果先前已註冊，您可以略過上述步驟：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
當狀態顯示為已註冊時，請使用 'az provider register' 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>建立 AKS 叢集

如果您已有符合上述需求的 AKS 叢集，請[跳至現有的叢集小節](#existing-cluster)，以新增機密運算節點集區。

首先，使用 az group create 命令來建立 叢集的資源群組。 下列範例會在 westus2 區域建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

現在，使用 az aks create 命令來建立 AKS 叢集。

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
上述會使用系統節點集區建立新的 AKS 叢集。 現在繼續新增 AKS (DCsv2) 上機密運算節點集區類型的使用者節點

下列範例會新增 3 個具有 `Standard_DC2s_v2` 大小節點的使用者節點集區。 您可以從[這裡](../virtual-machines/dcv2-series.md)選擇其他支援的 DCsv2 SKU 清單和區域：

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
上述命令應該會新增在此節點集區自動執行兩個精靈集的 **DC<x>s-v2** 新節點集區 - ([SGX 裝置外掛程式](confidential-nodes-aks-overview.md#sgx-plugin) & [SGX 報價協助程式](confidential-nodes-aks-overview.md#sgx-quote))

使用 az aks get-credentials 命令取得 AKS 叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
使用 kubectl get pods & nodes 命令，確認已正確建立節點，且已在 **DC<x>s-v2** 節點集區上執行 SGX 的相關精靈集，如下所示：

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
如果輸出符合上述項目，表示您的 AKS 叢集現已做好執行機密應用程式的準備。

移至[記憶體保護區中的 Hello World](#hello-world) 部署小節，以測試記憶體保護區中的應用程式。 或者，請依照下列指示，在 AKS 上新增其他節點集區 (AKS 支援混用 SGX 節點集區和非 SGX 節點集區)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>將機密運算節點集區新增至現有的 AKS 叢集<a id="existing-cluster"></a>

本節假設您已有執行中的 AKS 叢集符合必要條件一節所列的條件。

首先，請將功能新增至 Azure 訂用帳戶

```azurecli-interactive
az feature register --name AKS-ConfidentialComputinAddon --namespace Microsoft.ContainerService
```
可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 'az feature list' 命令檢查註冊狀態。 每個訂用帳戶只需註冊此功能一次。 如果先前已註冊，您可以略過上述步驟：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
當狀態顯示為已註冊時，請使用 'az provider register' 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

接下來，在現有的叢集上啟用與機密運算相關的 AKS 附加元件：

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
現在將 **DC<x>s-v2** 使用者節點集區新增至叢集
    
> [!NOTE]
> 若要使用機密計算功能，現有的 AKS 叢集必須至少有一個以 VM SKU 為基礎的 **DC<x>s-v2** 節點集區。 若要深入了解機密計算 DCsv2 VMs SKU，請參閱[可用的 SKU 和支援的區域](virtual-machine-solutions.md)。
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
輸出應該會顯示在 AKS 叢集上新增的 confcompool1。

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
如果輸出符合上述項目，表示您的 AKS 叢集現已做好執行機密應用程式的準備。

## <a name="hello-world-from-isolated-enclave-application"></a>隔離的記憶體保護區應用程式中的 Hello World <a id="hello-world"></a>
建立名為 hello-world-enclave.yaml 的檔案，並貼上下列 YAML 資訊清單。 這個 Open Enclave 範例應用程式程式碼可以在 [Open Enclave 專案](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)中找到。 下列部署會假設您已部署 "confcom" 附加元件。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

現在，使用 kubectl apply 命令，建立將在安全記憶體保護區中啟動的範例作業，如下列範例輸出所示：

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

您可以執行下列命令，以確認工作負載已成功建立受信任執行環境 (記憶體保護區)：

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>清除資源

若要移除相關聯的節點集區或刪除 AKS 叢集，請使用下列命令：

刪除 AKS 叢集
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>下一步

透過機密容器以機密的方式執行 Python、Node 等應用程式，方法是造訪[機密容器範例](https://github.com/Azure-Samples/confidential-container-samples)。

造訪[記憶體保護區感知 Azure 容器範例](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)，以執行記憶體保護區感知應用程式。
