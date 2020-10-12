---
title: 移除委派的存取權
description: 瞭解如何移除已委派給服務提供者進行 Azure Lighthouse 之資源的存取權。
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 72a2c49dde8cccfcc298d4128384a10bb7e8840f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167217"
---
# <a name="remove-access-to-a-delegation"></a>移除委派的存取權

將客戶的訂用帳戶或資源群組委派給 [Azure Lighthouse](../overview.md)的服務提供者之後，您可以視需要移除委派。 移除委派之後，先前授與服務提供者租使用者中使用者的 [Azure 委派資源管理](../concepts/azure-delegated-resource-management.md) 存取將不再適用。

只要使用者擁有適當的許可權，客戶租使用者或服務提供者租使用者中的使用者就可以完成移除委派。

> [!TIP]
> 雖然我們會在本主題中參考服務提供者和客戶，但 [管理多個](../concepts/enterprise.md) 租使用者的企業可以使用相同的程式。

## <a name="customers"></a>客戶

客戶租使用者中擁有訂用帳戶擁有者 [內建角色](../../role-based-access-control/built-in-roles.md#owner) 的使用者，可以 (或該訂用帳戶中的資源群組) ，移除該訂用帳戶的服務提供者存取權。 若要這樣做，客戶租使用者中的使用者可以移至 Azure 入口網站的 [ [服務提供者] 頁面](view-manage-service-providers.md#add-or-remove-service-provider-offers) ，在 [ **服務提供者供應** 專案] 畫面上尋找供應專案，然後選取該供應專案資料列中的垃圾桶圖示。

確認刪除之後，服務提供者租使用者中的任何使用者都無法存取先前已委派的資源。

## <a name="service-providers"></a>服務提供者

如果管理租使用者中的使用者被授與客戶資源的 [受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ，則可以移除對委派資源的存取權。 如果未將此角色指派給任何服務提供者使用者，則只有客戶的租使用者中的使用者可以移除委派。

下列範例顯示授與 **受控服務註冊指派刪除角色** 的指派，在上線 [過程](onboard-customer.md)中可包含在參數檔案中：

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

[建立受控服務供應](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization)專案以發佈至 Azure Marketplace 時，也可以在**授權**中選取此角色。

擁有此權限的使用者可以使用下列其中一種方式移除委派。

### <a name="azure-portal"></a>Azure 入口網站

1. 瀏覽至 [[我的客戶] 頁面](view-manage-customers.md)。
2. 選取 [ **委派**]。
3. 尋找您想要移除的委派，然後選取出現在其資料列中的垃圾桶圖示。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)。
- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](view-manage-customers.md)。
