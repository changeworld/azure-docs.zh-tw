---
title: 在 Azure 庫伯奈斯服務中使用託管標識
description: 瞭解如何在 Azure 庫伯奈斯服務 （AKS） 中使用託管標識
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369960"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure 庫伯奈斯服務中使用託管標識

目前，Azure Kubernetes 服務 （AKS） 群集（特別是 Kubernetes 雲供應商）需要*服務主體*才能在 Azure 中創建其他資源，如負載等化器和託管磁片。 您必須提供服務主體，要麼 AKS 代表您創建服務主體。 服務主體通常具有到期日期。 群集最終達到必須續訂服務主體以保持群集工作的狀態。 管理服務主體會增加複雜性。

*託管標識*本質上是圍繞服務主體的包裝，並簡化其管理。 要瞭解更多資訊，請閱讀[Azure 資源的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

AKS 創建兩個託管標識：

- **系統分配的託管標識**：Kubernetes 雲提供程式用於代表使用者創建 Azure 資源的標識。 系統分配的標識的生命週期與群集的生命週期相關聯。 刪除群集時，標識將被刪除。
- **使用者分配的託管標識**：用於群集中授權的標識。 例如，使用者分配的標識用於授權 AKS 使用 Azure 容器註冊表 （ACL），或授權 kubelet 從 Azure 獲取中繼資料。

載入項還會使用託管標識進行身份驗證。 對於每個載入項，託管標識由 AKS 創建，並持續載入項的生命週期。 要創建和使用資源位於MC_* 資源組之外的您自己的 VNet、靜態 IP 位址或附加的 Azure 磁片，請使用群集的主體 ID 執行角色指派。 有關角色指派的詳細資訊，請參閱[委派對其他 Azure 資源的存取權限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。

## <a name="before-you-begin"></a>開始之前

您必須安裝以下資源：

- Azure CLI，版本 2.2.0 或更高版本

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用託管標識創建 AKS 群集

現在，您可以使用以下 CLI 命令創建具有託管標識的 AKS 群集。

首先，創建 Azure 資源組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然後，創建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

使用託管標識成功創建群集包含此服務主體設定檔資訊：

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

最後，獲取訪問群集的憑據：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

群集將在幾分鐘內創建。 然後，您可以將應用程式工作負載部署到新群集，並與它進行交互，就像使用基於服務主體的 AKS 群集一樣。

> [!IMPORTANT]
>
> - 只能在創建群集期間啟用具有託管標識的 AKS 群集。
> - 無法更新或升級現有 AKS 群集以啟用託管標識。
