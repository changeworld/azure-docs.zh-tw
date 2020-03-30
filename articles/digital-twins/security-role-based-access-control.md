---
title: 瞭解基於角色的存取控制 - Azure 數位孿生 |微軟文檔
description: 瞭解 Azure 數位孿生中基於角色的存取控制和版權管理。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044956"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Azure Digital Twins 中的角色型存取控制

Azure 數位孿生支援對空間圖中的特定資料、資源和操作進行精確存取控制。 它通過稱為[基於角色的存取控制](https://docs.microsoft.com/azure/role-based-access-control/)（RBAC） 的細微性角色和版權管理來這樣做。 RBAC 是由「角色」__ 和「角色指派」__ 所組成的。 角色會識別權限的層級。 角色指派會將角色與使用者或裝置產生關聯。

使用 RBAC，就可以將權限授與：

- 使用者。
- 裝置。
- 服務主體。
- 使用者定義函式。
- 隸屬於網域的所有使用者。
- 租用戶。

存取程度也可以微調。

RBAC 的特色在於其權限會向下繼承到空間圖形。

## <a name="what-can-i-do-with-rbac"></a>RBAC 有何用途？

開發人員可能會使用 RBAC 來進行下列動作：

- 為使用者授與管理整棟建築物各項裝置的能力，或僅就特定房間或樓層進行授權。
- 為系統管理員授與整個圖形內所有空間圖形節點的全域存取權，或僅就某部分的圖形進行授權。
- 為支援專員授與圖形的讀取權限，但存取金鑰除外。
- 為網域的每個成員授與所有圖形物件的讀取權限。

## <a name="rbac-best-practices"></a>RBAC 最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定義

角色定義是權限和其他可構成角色之屬性的集合。 角色定義會列出允許的作業，包括任何具有該角色的物件可能執行的「建立」**、「讀取」**、「更新」** 和「刪除」**。 它還指定許可權應用於哪些物件類型。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> 若要擷取上述角色的完整定義，請查詢系統/角色 API。
> 若要深入了解，請閱讀[建立和管理角色指派](./security-create-manage-role-assignments.md#retrieve-all-roles)。

### <a name="object-identifier-types"></a>物件識別碼類型

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> 若要了解如何為服務主體授與權限，請閱讀[建立和管理角色指派](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)。

下列參考文件文章說明：

- 如何[查詢使用者的物件識別碼](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0) \(英文\)。
- 如何[取得服務主體的物件識別碼](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal) \(英文\)。
- 如何[擷取 Azure AD 租用戶的物件識別碼](../active-directory/develop/quickstart-create-new-tenant.md)。

## <a name="role-assignments"></a>角色指派

Azure Digital Twins 的角色指派會建立物件 (例如，使用者或 Azure AD 租用戶) 與角色和空間的關聯性。 權限會授與屬於該空間的所有物件。 空間包含其下方的整個空間圖形。

例如，假設某個使用者在空間圖形根節點 (代表建築物) 的角色指派中獲得了 `DeviceInstaller` 角色。 該使用者接著就能針對該節點及建築物中的所有其他子空間，讀取和更新裝置。

若要將權限授與收件者，請建立角色指派。 若要撤銷權限，請移除角色指派。

>[!IMPORTANT]
> 若要深入了解角色指派，請閱讀[建立和管理角色指派](./security-create-manage-role-assignments.md)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何建立和管理 Azure Digital Twins 角色指派，請閱讀[建立和管理角色指派](./security-create-manage-role-assignments.md)。

- 閱讀有關[Azure 的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/)的更多內容。
