---
title: 建立已啟用 Azure Arc 的上線服務主體 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: '建立已啟用 Azure Arc 的上線服務主體 '
keywords: Kubernetes, Arc, Azure, 容器
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050077"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>建立已啟用 Azure Arc 的上線服務主體 (預覽)

## <a name="overview"></a>概觀

您可以使用具備權限有限之角色指派的服務主體，來將 Kubernetes 叢集上線至 Azure Arc。這在持續整合和持續部署 (CI/CD) 管線 (例如 Azure Pipelines 和 GitHub Actions) 中很實用。

下列步驟提供使用服務主體來將 Kubernetes 叢集上線至 Azure Arc 的逐步解說。

## <a name="create-a-new-service-principal"></a>建立新的服務主體

使用資訊性名稱來建立新的服務主體。 請注意，此名稱對您的 Azure Active Directory 租用戶必須是唯一的：

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**輸出：**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>指派權限

建立新的服務主體之後，請將 "Kubernetes Cluster-Azure Arc 上線" 角色指派給新建立的主體。 這是權限有限的內建 Azure 角色，只允許該主體向 Azure 註冊叢集。 該主體無法更新、刪除或修改訂用帳戶內的任何其他叢集或資源。

透過有限的功能，客戶可以輕鬆地重複使用此主體來將多個叢集上線。

指派角色時，可能會藉由傳遞適當的 `--scope` 引數，進一步限制權限。 這讓客戶能夠限制叢集註冊。 各種 `--scope` 參數都支援下列案例：

| 資源  | `scope` 引數| 效果 |
| ------------- | ------------- | ------------- |
| 訂用帳戶 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 服務主體可以在指定的訂用帳戶中，於現有的資源群組中註冊任何叢集 |
| 資源群組 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 服務主體__只能__在資源群組 `myGroup` 中註冊叢集 |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**輸出：**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>搭配 Azure CLI 使用服務主體

參考新建立的服務主體：

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>後續步驟

* [使用 Azure 原則來控管叢集設定](./use-azure-policy.md)
