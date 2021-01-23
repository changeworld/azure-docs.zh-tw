---
title: Azure Active Directory 中的管理單位 | Microsoft Docs
description: 使用管理單位在 Azure Active Directory 中進行更細微的權限委派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82be52cff824c69dd2fe4ea5e7e1c0d7489be1d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740324"
---
# <a name="administrative-units-in-azure-active-directory"></a>Azure Active Directory 中的管理單位

本文說明 Azure Active Directory (Azure AD) 中的管理單位。 管理單位是一種 Azure AD 資源，可以是其他 Azure AD 資源的容器。 管理單位只能包含使用者和群組。

管理單位會將角色中的權限限制為您所定義之組織的任何部分。 例如，您可以使用管理單位，將[服務台管理員](permissions-reference.md#helpdesk-administrator)角色委派給區域支援專家，讓他們只能管理其支援區域中的使用者。

## <a name="deployment-scenario"></a>部署案例

在由任何種類的獨立部門所構成的組織中，使用管理單位來限制系統管理範圍可能非常有用。 假設有一家由許多自主運作之學院 (商學院、工學院等) 所組成的大型大學。 每個學院都有一個 IT 管理員小組，負責控制存取、管理使用者，以及為學校設定原則。

中央系統管理員可以：

- 建立一個角色，該角色只對商學院管理單位中的 Azure AD 使用者有系統管理權限。
- 建立商學院的管理單位。
- 只在管理單位中填入商學院學生和職員。
- 將商學院 IT 小組新增至其範圍內的角色。

## <a name="license-requirements"></a>授權需求

若要使用管理單位，每個管理單位的管理員都需要有 Azure Active Directory Premium 授權，而管理單位的成員則需要有 Azure Active Directory Free 授權。 如需詳細資訊，請參閱〈 [開始使用 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)〉。

## <a name="manage-administrative-units"></a>管理管理單位

您可以使用 Azure 入口網站、PowerShell Cmdlet 和指令碼或 Microsoft Graph 來管理管理單位。 如需詳細資訊，請參閱：

- [在管理單位中建立、移除、填入和新增角色](admin-units-manage.md)：包含完整的操作程序。
- [使用管理單位](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)：涵蓋如何使用 PowerShell 處理管理單位。
- [管理單位 Graph 支援](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true)：提供管理單位的 Microsoft Graph 詳細文件。

### <a name="plan-your-administrative-units"></a>規劃您的管理單位

管理單位可用來以邏輯方式將 Azure AD 資源分組。 IT 部門分散在全球各地的組織，可建立管理單位以定義相關地理界限。 另一個案例中，跨國組織具有半自主運作的子組織，則可用管理單位來表示這些子組織。

組織的獨特需求將會引導管理單位的建立準則。 管理單位是跨 Microsoft 365 服務定義結構的常見方式。 我們建議您在準備管理單位時考量到其跨 Microsoft 365 服務使用的特性。 如果您可讓管理單位之下跨 Microsoft 365 的通用資源產生關聯，即可從管理單位獲得最大價值。

您可預期在組織中建立管理單位，以進行下列階段：

1. **初步採用**：您的組織會根據初始準則開始建立管理單位，而管理單位數目會隨著準則的精簡而增加。
1. **裁撤**：定義好準則後，將會刪除不再需要的管理單位。
1. **穩定**：您的組織結構已妥善定義，而且管理單位數目在短期內不會大幅變更。

## <a name="currently-supported-scenarios"></a>目前支援的案例

身為全域管理員或特殊權限角色管理員，您可以使用 Azure AD 入口網站執行下列動作：

- 建立管理單位
- 新增管理單位的使用者和群組成員
- 將 IT 人員指派給管理單位範圍的系統管理員角色。

管理單位範圍的系統管理員可使用 Microsoft 365 系統管理中心，在其管理單位中進行使用者的基本管理。 具有管理單位範圍的群組管理員，可使用 PowerShell、Microsoft Graph 和 Microsoft 365 系統管理中心來管理群組。

>[!Note]
>只有本節中說明的功能可在 Microsoft 365 系統管理中心使用。 具有管理單位範圍的 Azure AD 角色無法使用組織層級功能。

以下幾節說明目前對管理單位案例的支援。

### <a name="administrative-unit-management"></a>管理單位的管理

| 權限 |   Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心 |
| --- | --- | --- | --- |
| 建立和刪除管理單位   |    支援    |   支援   |    不支援 |
| 個別新增和移除管理單位成員    |   支援    |   支援   |    不支援 |
| 使用 CSV 檔案大量新增和移除管理單位成員   |    不支援     |  支援   |    沒有支援計劃 |
| 指派管理單位範圍的系統管理員  |     支援    |   支援    |   不支援 |
| 根據屬性動態新增和移除管理單位成員 | 不支援 | 不支援 | 不支援

### <a name="user-management"></a>使用者管理

| 權限 |   Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心 |
| --- | --- | --- | --- |
| 管理單位範圍的使用者屬性、密碼和授權管理   |    支援     |  支援   |   支援 |
| 管理單位範圍的使用者登入封鎖和解除封鎖    |   支援   |    支援   |    支援 |
| 管理單位範圍的使用者多重要素驗證認證管理   |    支援   |   支援   |   不支援 |

### <a name="group-management"></a>群組管理

| 權限 |   Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心 |
| --- | --- | --- | --- |
| 管理單位範圍的群組屬性和成員管理     |  支援   |    支援    |  不支援 |
| 管理單位範圍的群組授權管理   |    支援  |    支援   |   不支援 |

管理單位只會將範圍套用至管理權限。 其不會阻止成員或系統管理員使用其[預設使用者權限](../fundamentals/users-default-permissions.md)來瀏覽管理單位以外的其他使用者、群組或資源。 在 Microsoft 365 系統管理中心，系統會篩選出範圍管理員之管理單位以外的使用者。但您可以在 Azure AD 入口網站、PowerShell 和其他 Microsoft 服務中瀏覽其他使用者。

## <a name="next-steps"></a>後續步驟

- [管理管理單位](admin-units-manage.md)
- [管理管理單位中的使用者](admin-units-add-manage-users.md)
- [管理管理單位中的群組](admin-units-add-manage-groups.md)
- [將範圍角色指派至管理單位](admin-units-assign-roles.md)
