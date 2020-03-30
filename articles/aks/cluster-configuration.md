---
title: Azure 庫伯奈斯服務 （AKS） 中的群集配置
description: 瞭解如何在 Azure 庫伯奈斯服務 （AKS） 中配置群集
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479159"
---
# <a name="configure-an-aks-cluster"></a>設定 AKS 叢集

作為創建 AKS 群集的一部分，您可能需要自訂群集配置以滿足您的需要。 本文介紹了一些用於自訂 AKS 群集的選項。

## <a name="os-configuration-preview"></a>作業系統配置（預覽版）

AKS 現在支援 Ubuntu 18.04 作為預覽中的節點作業系統 （OS）。 在預覽期間，Ubuntu 16.04 和 Ubuntu 18.04 均可用。

您必須安裝以下資源：

- Azure CLI，版本 2.2.0 或更高版本
- aks 預覽 0.4.35 擴展

要安裝 aks 預覽 0.4.35 擴展或更高版本，請使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

註冊功能`UseCustomizedUbuntuPreview`：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

狀態可能需要幾分鐘才能顯示為 **"已註冊**"。 您可以使用[az 要素清單](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 az`Microsoft.ContainerService`提供程式寄存器命令刷新資來源提供者[的註冊](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

將群集配置為在創建群集時使用 Ubuntu 18.04。 使用標誌`--aks-custom-headers`將 Ubuntu 18.04 設置為預設作業系統。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果要創建常規 Ubuntu 16.04 群集，可以通過省略自訂`--aks-custom-headers`標記來執行此操作。

## <a name="custom-resource-group-name"></a>自訂資源組名稱

在 Azure 中部署 Azure Kubernetes 服務群集時，將為輔助節點創建第二個資源組。 預設情況下，AKS 將命名節點資源組`MC_resourcegroupname_clustername_location`，但您也可以提供您自己的名稱。

要指定自己的資源組名稱，請安裝 aks 預覽 Azure CLI 擴展版本 0.3.2 或更高版本。 使用 Azure CLI，使用`--node-resource-group``az aks create`命令的參數為資源組指定自訂名稱。 如果使用 Azure 資源管理器範本來部署 AKS 群集，則可以使用 屬性`nodeResourceGroup`定義資源組名稱。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

輔助資源組由 Azure 資來源提供者在您自己的訂閱中自動創建。 請注意，只能在創建群集時指定自訂資源組名稱。 

使用節點資源組時，請記住，您不能：

- 為節點資源組指定現有資源組。
- 為節點資源組指定不同的訂閱。
- 創建群集後更改節點資源組名稱。
- 指定節點資源組中的託管資源的名稱。
- 修改或刪除節點資源組中的託管資源的 Azure 創建的標記。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用`Kured`將[安全和內核更新應用於](node-updates-kured.md)群集中的 Linux 節點。
- 請參閱[升級 Azure 庫伯奈斯服務 （AKS） 群集](upgrade-cluster.md)，瞭解如何將群集升級到最新版本的 Kubernetes。
- 查看[有關 AKS](faq.md)的常見問題清單，以查找一些常見 AKS 問題的答案。