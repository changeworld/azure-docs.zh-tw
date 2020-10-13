---
title: 如何在 Azure API 管理中使用角色型存取控制 | Microsoft Docs
description: 了解如何在 Azure API 管理中使用內建角色和建立自訂角色
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: faef2721b48ffab12264c585d2dec55ab9334016
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015284"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>如何在 Azure API 管理中使用角色型存取控制

Azure API 管理依賴 Azure 角色型存取控制 (Azure RBAC) ，以針對 API 管理服務和 (實體啟用更細緻的存取管理，例如 Api 和原則) 。 本文章將為您提供 API 管理中內建角色和自訂角色的概觀。 如需有關 Azure 入口網站中的存取管理的詳細資訊，請參閱[開始在 Azure 入口網站中使用存取管理](../role-based-access-control/overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>內建角色

API 管理目前提供三個內建角色，不久之後會再新增兩個角色。 這些角色可以指派到不同的範圍，包括訂用帳戶、資源群組，以及個別 API 管理執行個體。 比方說，如果您將「API 管理服務讀者」角色指派給資源群組層級的使用者，則使用者會擁有資源群組內所有 API 管理實例的讀取權限。 

下表提供內建角色的簡短描述。 您可以使用 Azure 入口網站或其他工具 (包括 Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md) 和 [REST API](../role-based-access-control/role-assignments-rest.md)) 指派這些角色。 如需如何指派內建角色的詳細資料，請參閱[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)。

| 角色          | 讀取權限<sup>[1]</sup> | 寫入權限<sup>[2]</sup> | 服務建立、刪除、調整規模、VPN 及自訂網域設定 | 存取舊版發行者入口網站 | 說明
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API 管理服務參與者 | ✓ | ✓ | ✓ | ✓ | 進階使用者。 具有 API 管理服務及實體 (例如 API 和原則) 的完整 CRUD 存取。 具有舊版發行者入口網站的存取權限。 |
| API 管理服務讀取器 | ✓ | | || 具有 API 管理服務及實體的唯讀權限。 |
| API 管理服務操作員 | ✓ | | ✓ | | 可以管理 API 管理服務，但無法管理實體。|
| API 管理服務編輯器<sup>*</sup> | ✓ | ✓ | |  | 可以管理 API 管理實體，但無法管理服務。|
| API 管理內容管理員<sup>*</sup> | ✓ | | | ✓ | 可以管理開發人員入口網站。 具有服務和實體的唯讀權限。|

<sup>[1] API 管理服務及實體 (例如 API 和原則) 的讀取權限</sup>

<sup>[2] API 管理服務及實體的寫入權限，下列作業除外：建立、刪除和調整執行個體；VPN 設定；以及自訂網域設定。</sup>

<sup>\* 將現有發行者入口網站中的所有系統管理 UI 遷移至 Azure 入口網站之後，就可以使用「服務編輯器」角色。在發行者入口網站重構為僅包含管理開發人員入口網站的相關功能之後，就可以使用「內容管理員」角色。</sup>  

## <a name="custom-roles"></a>自訂角色

如果內建角色都無法滿足您的特定需求，您可以建立自訂角色來提供更精細的 API 管理實體存取管理。 例如，您可以建立自訂角色，使其具有 API 管理服務的唯讀權限，但只有單一特定 API 的寫入權限。 若要深入了解自訂角色，請參閱 [Azure RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)。 

> [!NOTE]
> 若要能夠在 Azure 入口網站中查看 API 管理實例，自訂角色必須包含 ```Microsoft.ApiManagement/service/read``` 動作。

當您建立自訂角色時，從其中一個內建角色開始會比較輕鬆。 編輯屬性來新增 **Actions**、**NotActions** 或 **AssignableScopes**，然後將變更另存為新角色。 下列範例會以「API 管理服務讀者」角色開始，並建立名為「計算機 API 編輯器」的自訂角色。 您可以將自訂角色指派給特定 API。 因此，這個角色只能存取該 API。 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)一文包含可在 API 管理層級上授與的權限清單。

## <a name="video"></a>影片


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 中的角色型存取控制，請參閱下列文章：
  * [開始在 Azure 入口網站中使用存取管理](../role-based-access-control/overview.md)
  * [使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC 中的自訂角色](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) (機器翻譯)
