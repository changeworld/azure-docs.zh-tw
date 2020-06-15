---
title: Azure Kubernetes Service (AKS) 的叢集設定
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定叢集
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725141"
---
# <a name="configure-an-aks-cluster"></a>設定 AKS 叢集

建立 AKS 叢集的過程中，可能需要自訂叢集設定來配合您的需求。 本文會介紹幾種可用來自訂 AKS 叢集的選項。

## <a name="os-configuration-preview"></a>作業系統設定 (預覽)

AKS 現可支援 Ubuntu 18.04 做為預覽的節點作業系統 (OS)。 在預覽期間，Ubuntu 16.04 和 Ubuntu 18.04 皆可供使用。

您必須先安裝下列資源：

- Azure CLI 2.2.0 版或更新版本
- aks-preview 0.4.35 擴充功能

若要安裝 aks-preview 0.4.35 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

註冊 `UseCustomizedUbuntuPreview` 功能：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>新叢集

在叢集建立時，請將叢集設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標將 Ubuntu 18.04 設定為預設作業系統。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果想要建立一般的 Ubuntu 16.04 叢集，省略自訂的 `--aks-custom-headers` 標籤即可。

### <a name="existing-clusters"></a>現有叢集

請將新的節點集區設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標，將 Ubuntu 18.04 設定為該節點集區的預設作業系統。

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果想要建立一般的 Ubuntu 16.04 節點集區，省略自訂的 `--aks-custom-headers` 標籤即可。


## <a name="custom-resource-group-name"></a>自訂資源群組名稱

當您在 Azure 中部署 Azure Kubernetes Service 叢集時，系統會建立背景工作角色節點的第二個資源群組。 根據預設，AKS 會將該節點資源群組命名為 `MC_resourcegroupname_clustername_location`，但也可以自行命名。

若要指定屬於自己的資源群組名稱，請安裝 aks-preview Azure CLI 擴充功能 0.3.2 或以後版本。 若要使用 Azure CLI，請使用 `az aks create` 命令的 `--node-resource-group` 參數指定資源群組的自訂名稱。 如果使用 Azure Resource Manager 範本部署 AKS 叢集，則可使用 `nodeResourceGroup` 屬性來定義資源群組名稱。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

您自己訂閱內的 Azure 資源提供者，會自動建立次要資源群組。 建立叢集時，您只能指定自訂資源群組的名稱。 

當使用節點資源群組時，您無法進行以下動作：

- 指定現有的資源群組做為節點資源群組。
- 為節點資源群組指定不同的訂閱。
- 在建立叢集之後，變更節點資源群組名稱。
- 指定節點資源群組內受控資源的名稱。
- 修改或刪除節點資源群組內由 Azure 建立的受控資源標籤。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 `Kured`，在您的叢集中[將安全性和核心更新套用至 Linux 節點](node-updates-kured.md)。
- 請參閱[升級 Azure Kubernetes Service (AKS) 叢集](upgrade-cluster.md)，了解如何將叢集升級至最新版本的 Kubernetes。
- 關於 AKS 常見問題的解答，請參閱 [AKS 的常見問題集](faq.md)。