---
title: 管理單位管理 (預覽) - Azure AD | Microsoft Docs
description: 使用管理單位在 Azure Active Directory 中進行更細微的權限委派
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5854ef57a08218fee4479eb9e075824f4a1ff781
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376378"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>在 Azure Active Directory 中的管理單位管理 (預覽)

本文說明 Azure Active Directory (Azure AD) 中的管理單位。 管理單位是一種 Azure AD 資源，可以是其他 Azure AD 資源的容器。 在此預覽版本中，管理單位只能包含使用者和群組。

管理單位可讓您授與系統管理權限，這些權限受限於您所定義組織的部門、區域或其他區段。 您可以使用管理單位將權限委派給區域管理員或以細微的層級設定原則。 例如，使用者帳戶管理員可以更新設定檔資訊、重設密碼，且只針對其管理單位中的使用者指派授權。

例如，委派給地區支援專家的[服務台管理員](directory-assign-admin-roles.md#helpdesk-administrator)角色，受限於僅只管理其所支援區域中的使用者。

## <a name="deployment-scenario"></a>部署案例

在由任何種類的獨立部門所構成的組織中，使用管理單位來限制系統管理範圍可能非常有用。 假設有一家由許多自主學院 (商學院、工學院等等) 所組成的大型大學，而每個學院都有一個 IT 系統管理員小組，負責控制存取、管理使用者，以及為其學院設定原則。 中央系統管理員可以：

- 建立一個角色，該角色只對商學院管理單位中的 Azure AD 使用者有系統管理權限
- 建立商學院的管理單位
- 只在管理單位中填入商學院學生和職員
- 將商學院 IT 小組新增至其範圍內的角色

## <a name="license-requirements"></a>授權需求

使用管理單位時，每個管理單位的管理員都需要有 Azure Active Directory Premium 授權，而管理單位的成員則需要有 Azure Active Directory Free 授權。 如需詳細資訊，請參閱〈 [開始使用 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)〉。

## <a name="manage-administrative-units"></a>管理管理單位

在此預覽版本中，您可使用 Azure 入口網站、PowerShell Cmdlet 和指令碼或 Microsoft Graph 來管理管理單位。 您可以參考我們的文件以取得詳細資料：

- [在管理單位中建立、移除、填入和新增角色](roles-admin-units-manage.md)：完成操作程序
- [使用管理單位](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)：如何使用 PowerShell 處理管理單位
- [管理單位 Graph 支援](/graph/api/resources/administrativeunit?view=graph-rest-beta)：管理單位的 Microsoft Graph 詳細文件。

### <a name="planning-your-administrative-units"></a>規劃您的管理單位

管理單位可用於以邏輯方式將 Azure AD 資源分組。 例如，對於 IT 部門分散在全球各地的組織而言，建立可定義這些地理界限的管理單位可能很合理。 在多國組織具有不同「子組織」(半自主運作) 的另一種情況下，可能會以管理單位來表示每個子組織。

組織的獨特需求將會引導管理單位的建立準則。 管理單位是跨 Microsoft 365 服務定義結構的常見方式。 我們建議您在準備管理單位時考量到其跨 Microsoft 365 服務使用的特性。 如果您可讓管理單位之下跨 Microsoft 365 的通用資源產生關聯，即可從管理單位獲得最大價值。

您可預期在組織中建立管理單位，以進行下列階段：

1. 初始採用：您的組織會根據初始準則開始建立管理單位，而管理單位數目會隨著準則的精簡而增加。
1. 剪除：定義好準則後，將會刪除不再需要的管理單位。
1. 穩定：您的組織結構已妥善定義，而且管理單位數目不會在短期內大幅變更。

## <a name="currently-supported-scenarios"></a>目前支援的案例

全域管理員或特殊權限的角色管理員可以使用 Azure AD 入口網站來建立管理單位、將使用者新增為管理單位的成員，然後將 IT 人員指派給管理單位範圍的系統管理員角色。 管理單位範圍的系統管理員可接著使用 Microsoft 365 系統管理中心，在其管理單位中進行使用者的基本管理。

此外，您可將群組新增為管理單位的成員，而管理單位範圍的群組管理員可使用 PowerShell、Microsoft Graph 和 Azure AD 入口網站來管理成員。

下表說明目前對管理單位案例的支援。

### <a name="administrative-unit-management"></a>管理單位的管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
建立和刪除管理單位   |    支援    |   支援   |    不支援
個別新增和移除管理單位成員    |   支援    |   支援   |    不支援
使用 .csv 檔案大量新增和移除管理單位成員   |    不支援     |  支援   |    沒有支援計劃
指派管理單位範圍的系統管理員  |     支援    |   支援    |   不支援
根據屬性動態新增和移除 AU 成員 | 不支援 | 不支援 | 不支援

### <a name="user-management"></a>使用者管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
管理單位範圍的使用者屬性、密碼、授權管理   |    支援     |  支援   |   支援
管理單位範圍的使用者登入封鎖和解除封鎖    |   支援   |    支援   |    支援
管理單位範圍的使用者 MFA 認證管理   |    支援   |   支援   |   不支援

### <a name="group-management"></a>群組管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
管理單位範圍的群組屬性和成員管理     |  支援   |    支援    |  不支援
管理單位範圍的群組授權管理   |    支援  |    支援   |   不支援

> [!NOTE]
>
> 具有管理單位範圍的系統管理員無法管理動態群組成員資格規則。

管理單位只會將範圍套用至管理權限。 其不會阻止成員或系統管理員使用其[預設使用者權限](../fundamentals/users-default-permissions.md)來瀏覽管理單位以外的其他使用者、群組或資源。 在 Microsoft 365 系統管理中心，系統會篩選出限域系統管理員的管理單位以外的使用者，但您可以在 Azure AD 入口網站、PowerShell 和其他 Microsoft 服務中瀏覽其他使用者。

## <a name="next-steps"></a>後續步驟

- [管理 AU](roles-admin-units-manage.md)
- [在 AU 中管理使用者](roles-admin-units-add-manage-users.md)
- [在 AU 中管理群組](roles-admin-units-add-manage-groups.md)
- [將限域角色指派給 AU](roles-admin-units-assign-roles.md)
