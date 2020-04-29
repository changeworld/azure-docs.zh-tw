---
title: Azure Kubernetes Services （AKS）中的叢集設定
description: 瞭解如何在 Azure Kubernetes Service 中設定叢集（AKS）
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479159"
---
# <a name="configure-an-aks-cluster"></a>設定 AKS 叢集

建立 AKS 叢集時，您可能需要自訂叢集設定以符合您的需求。 本文介紹自訂 AKS 叢集的幾個選項。

## <a name="os-configuration-preview"></a>OS 設定（預覽）

AKS 現在支援 Ubuntu 18.04 做為預覽中的節點作業系統（OS）。 在預覽期間，可以使用 Ubuntu 16.04 和 Ubuntu 18.04。

您必須安裝下列資源：

- Azure CLI，2.2.0 或更新版本
- Aks-preview 0.4.35 延伸模組

若要安裝 aks-preview 0.4.35 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

註冊`UseCustomizedUbuntuPreview`功能：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為**已註冊**。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令來檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當 [狀態] 顯示為 [已註冊] 時， `Microsoft.ContainerService`請使用[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令重新整理資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

將叢集設定為在建立叢集時使用 Ubuntu 18.04。 使用`--aks-custom-headers`旗標將 Ubuntu 18.04 設定為預設 OS。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果您想要建立一般的 Ubuntu 16.04 叢集，可以省略自訂`--aks-custom-headers`標記來執行這項操作。

## <a name="custom-resource-group-name"></a>自訂資源組名

當您在 Azure 中部署 Azure Kubernetes Service 叢集時，會為背景工作角色節點建立第二個資源群組。 根據預設，AKS 會命名節點資源群組`MC_resourcegroupname_clustername_location`，但您也可以提供您自己的名稱。

若要指定您自己的資源組名，請安裝 aks-preview Azure CLI 延伸模組版本0.3.2 或更新版本。 使用 Azure CLI，使用`--node-resource-group` `az aks create`命令的參數來指定資源群組的自訂名稱。 如果您使用 Azure Resource Manager 範本來部署 AKS 叢集，您可以使用`nodeResourceGroup`屬性來定義資源組名。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

次要資源群組會由您自己的訂用帳戶中的 Azure 資源提供者自動建立。 請注意，您只能在建立叢集時指定自訂資源組名。 

當您使用 node 資源群組時，請記住，您不能：

- 為節點資源群組指定現有的資源群組。
- 為節點資源群組指定不同的訂用帳戶。
- 建立叢集之後，請變更節點資源組名。
- 指定節點資源群組內的受控資源名稱。
- 修改或刪除節點資源群組中 Azure 建立的受控資源標記。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用`Kured`將[安全性和核心更新套用至](node-updates-kured.md)叢集中的 Linux 節點。
- 請參閱[升級 Azure Kubernetes Service （AKS）](upgrade-cluster.md)叢集，以瞭解如何將您的叢集升級至最新版本的 Kubernetes。
- 請參閱[AKS](faq.md)的常見問題清單，以尋找一些常見 AKS 問題的解答。