---
title: 在 Azure Kubernetes Service 中使用受控識別
description: 瞭解如何在 Azure Kubernetes Service 中使用受控識別（AKS）
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121426"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>預覽-在 Azure Kubernetes Service 中使用受控識別

目前，Azure Kubernetes Service （AKS）叢集（特別是 Kubernetes 雲端提供者）需要*服務主體*來建立其他資源，例如 Azure 中的負載平衡器和受控磁片。 您必須提供服務主體，否則 AKS 會代表您建立一個。 服務主體通常會有到期日。 叢集最終會到達服務主體必須更新的狀態，才能讓叢集正常運作。 管理服務主體會增加複雜度。

*受控*識別基本上是服務主體的包裝函式，讓其管理變得更簡單。 若要深入瞭解，請參閱[Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

AKS 會建立兩個受控識別：

- **系統指派的受控識別**： Kubernetes 雲端提供者用來代表使用者建立 Azure 資源的身分識別。 系統指派之身分識別的生命週期會系結至叢集叢集。 刪除叢集時，會刪除身分識別。
- **使用者指派的受控識別**：用來在叢集中進行授權的身分識別。 例如，使用者指派的身分識別會用來授權 AKS 使用存取控制記錄（Acr），或授權 kubelet 從 Azure 取得中繼資料。

在此預覽期間，仍然需要服務主體。 它是用來授權附加元件，例如監視、虛擬節點、Azure 原則和 HTTP 應用程式路由。 正在進行工作，以移除服務主體名稱（SPN）上附加元件的相依性。 最後，將會完全移除 AKS 中的 SPN 需求。

> [!IMPORTANT]
> AKS preview 功能可在自助服務上自行選擇。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽僅由客戶支援以最佳方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需詳細資訊，請參閱下列支援文章：
>
> - [AKS 支援原則](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

您必須安裝下列資源：

- Azure CLI，2.0.70 或更新版本
- Aks-preview 0.4.14 延伸模組

若要安裝 aks-preview 0.4.14 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> 在訂用帳戶上註冊功能之後，您目前無法取消註冊該功能。 當您啟用某些預覽功能時，預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 相反地，請使用個別的訂用帳戶來測試預覽功能，並收集意見反應。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為**已註冊**。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令來檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

當 [狀態] 顯示為 [已註冊] 時，請使用[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>建立具有受控識別的 AKS 叢集

您現在可以使用下列 CLI 命令，建立具有受控識別的 AKS 叢集。

首先，建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然後，建立 AKS 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

最後，取得用來存取叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

叢集將會在幾分鐘內建立。 接著，您可以將應用程式工作負載部署到新的叢集，並與其互動，就像您使用以服務主體為基礎的 AKS 叢集所做的一樣。

> [!IMPORTANT]
>
> - 只有在叢集建立期間，才可以啟用具有受控識別的 AKS 叢集。
> - 無法更新或升級現有的 AKS 叢集，以啟用受控識別。
