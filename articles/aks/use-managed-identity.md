---
title: 在 Azure 庫伯奈斯服務中使用託管標識
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中使用託管識別
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 907aa83bc293aacd9920d8fd79a1b3184dd1d5dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767588"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure 庫伯奈斯服務中使用託管標識

目前,Azure Kubernetes 服務 (AKS) 群集(特別是 Kubernetes 雲提供程式)需要一個標識來創建其他資源,如 Azure 中的負載均衡器和託管磁碟,此標識可以是*託管標識*或*服務主體*。 如果使用[服務主體](kubernetes-service-principal.md),則必須提供一個或 AKS 代表您創建一個。 如果您使用託管標識,AKS 將自動為您創建此標識。 使用服務主體的群集最終達到必須續訂服務主體以保持群集工作的狀態。 管理服務主體會增加複雜性,這就是為什麼它更容易使用託管標識。 相同的許可權要求適用於服務主體和託管標識。

*託管標識*本質上是圍繞服務主體的包裝,並簡化其管理。 要瞭解更多資訊,請閱讀[Azure 資源的託管識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

AKS 建立兩個託管識別:

- **系統分配的託管標識**:Kubernetes 雲端提供程式用於代表使用者創建 Azure 資源的標識。 系統分配的標識的生命週期與群集的生命周期相關聯。 刪除群集時,標識將被刪除。
- **使用者分配的託管標識**:用於群集中授權的標識。 例如,使用者分配的標識用於授權 AKS 使用 Azure 容器註冊表 (ACL),或授權 kubelet 從 Azure 獲取中繼資料。

載入項還會使用託管標識進行身份驗證。 對於每個載入項,託管標識由 AKS 創建,並持續載入項的生命週期。 要建立和使用資源位於MC_* 資源組之外的您自己的 VNet、靜態 IP 位址或附加的 Azure 磁碟,請使用群集的主體 ID 執行角色分配。 有關角色分配的詳細資訊,請參閱[委派對其他 Azure 資源的存取許可權](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。

## <a name="before-you-begin"></a>開始之前

您必須安裝以下資源:

- Azure CLI,版本 2.2.0 或更高版本

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用託管識別建立 AKS 群集

現在,您可以使用以下 CLI 命令建立具有託管標識的 AKS 群集。

首先,建立 Azure 資源群組:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然後,建立 AKS 叢集:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

使用託管識別成功建立群組包含此服務主體設定檔資訊:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

最後,獲取訪問群集的認證:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

群集將在幾分鐘內創建。 然後,您可以將應用程式工作負載部署到新群集,並與它進行交互,就像使用基於服務主體的 AKS 群集一樣。

> [!IMPORTANT]
>
> - 只能在創建群集期間啟用具有託管標識的 AKS 群集。
> - 無法更新或升級現有 AKS 群集以啟用託管標識。
