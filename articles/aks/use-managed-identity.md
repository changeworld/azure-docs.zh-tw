---
title: 在 Azure Kubernetes Service 中使用受控識別
description: 瞭解如何在 Azure Kubernetes Service 中使用受控識別（AKS）
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112961"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes Service 中使用受控識別

目前，Azure Kubernetes Service （AKS）叢集（特別是 Kubernetes 雲端提供者）需要身分識別才能在 Azure 中建立額外的資源，例如負載平衡器和受控磁片，此身分識別可以是*受控識別*或*服務主體*。 如果您使用[服務主體](kubernetes-service-principal.md)，則必須提供一個或 AKS 代表您建立一個。 如果您使用受控識別，則會自動 AKS 來為您建立。 使用服務主體的叢集最終會到達必須更新服務主體的狀態，才能讓叢集正常運作。 管理服務主體會增加複雜性，這也是為什麼較容易使用受控識別的原因。 相同的許可權需求同時適用于服務主體和受控識別。

*受控*識別基本上是服務主體的包裝函式，讓其管理變得更簡單。 若要深入瞭解，請參閱[Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

AKS 會建立兩個受控識別：

- **系統指派的受控識別**： Kubernetes 雲端提供者用來代表使用者建立 Azure 資源的身分識別。 系統指派之身分識別的生命週期會系結至叢集叢集。 刪除叢集時，會刪除身分識別。
- **使用者指派的受控識別**：用來在叢集中進行授權的身分識別。 例如，使用者指派的身分識別會用來授權 AKS 使用 Azure Container registry （Acr），或授權 kubelet 從 Azure 取得中繼資料。

附加元件也會使用受控識別進行驗證。 針對每個附加元件，AKS 會建立受控識別，並在附加元件的生命週期內持續。 

## <a name="before-you-begin"></a>在您開始前

您必須安裝下列資源：

- Azure CLI，2.2.0 或更新版本

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

使用受控識別成功建立叢集包含此服務主體設定檔資訊：

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> 若要建立及使用您自己的 VNet、靜態 IP 位址或連結的 Azure 磁片，其中資源位於 MC_ * 資源群組之外，請使用叢集系統指派的受控識別 PrincipalID 來執行角色指派。 如需角色指派的詳細資訊，請參閱[委派其他 Azure 資源的存取權](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> 授與 Azure 雲端提供者所使用叢集受控識別的許可權，可能需要60分鐘的時間來填入。

最後，取得用來存取叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

叢集將會在幾分鐘內建立。 接著，您可以將應用程式工作負載部署到新的叢集，並與其互動，就像您使用以服務主體為基礎的 AKS 叢集所做的一樣。

> [!IMPORTANT]
>
> - 只有在叢集建立期間，才可以啟用具有受控識別的 AKS 叢集。
> - 無法更新或升級現有的 AKS 叢集，以啟用受控識別。
