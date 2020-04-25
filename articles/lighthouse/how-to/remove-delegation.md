---
title: 移除委派的存取權
description: 瞭解如何對已委派給 Azure 委派資源管理的服務提供者的資源，移除其存取權。
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: d0db809eb057f8b4bb48bdf9dd127f4d488f0406
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149446"
---
# <a name="remove-access-to-a-delegation"></a>移除委派的存取權

將客戶的訂用帳戶或資源群組委派給[Azure 委派資源管理](../concepts/azure-delegated-resource-management.md)的服務提供者之後，您可以視需要移除委派。 一旦移除委派，先前授與給服務提供者租使用者中之使用者的存取將不再適用。

只要使用者擁有適當的許可權，就可以在客戶租使用者或服務提供者租使用者中移除委派。

## <a name="customers"></a>客戶

客戶租使用者中擁有訂用帳戶之擁有者[內建角色](../../role-based-access-control/built-in-roles.md#owner)的使用者，可以移除該訂用帳戶（或該訂用帳戶中的資源群組）的服務提供者存取權。 若要這樣做，客戶租使用者中的使用者可以前往 [Azure 入口網站的 [[服務提供者] 頁面](view-manage-service-providers.md#add-or-remove-service-provider-offers)，在 [**服務提供者供應**專案] 畫面上尋找供應專案，然後選取該供應專案的資料列中的垃圾桶圖示。

確認刪除之後，服務提供者租使用者中的任何使用者都無法存取先前已委派的資源。

## <a name="service-providers"></a>服務提供者

如果已將客戶資源的「[受管理的服務註冊指派」角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)授與「管理租使用者」中的使用者，就可以移除這些委派資源的存取權。 如果此角色未指派給任何服務提供者使用者，則只能由客戶租使用者中的使用者移除委派。

下列範例顯示授與**受控服務註冊指派刪除角色**的指派，在上[架](onboard-customer.md)程式期間可以包含在參數檔案中：

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

[建立受管理的服務供應](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization)專案以發佈至 Azure Marketplace 時，也可以在**授權**中選取此角色。

擁有此權限的使用者可以使用下列其中一種方式移除委派。

### <a name="azure-portal"></a>Azure 入口網站

1. 瀏覽至 [[我的客戶] 頁面](view-manage-customers.md)。
2. 選取 [**委派**]。
3. 尋找您要移除的委派，然後選取出現在其資料列中的垃圾桶圖示。

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
- 前往 Azure 入口網站中的 [我的客戶]****，以[檢視及管理客戶](view-manage-customers.md)。
