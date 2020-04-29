---
title: 管理單位管理（預覽）-Azure AD |Microsoft Docs
description: 使用管理單位在 Azure Active Directory 中進行更細微的權限委派
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406479"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory 中的系統管理單位管理（預覽）

本文說明 Azure Active Directory （Azure AD）中的管理單位。 系統管理單位是 Azure AD 資源，可以是其他 Azure AD 資源的容器。 在此預覽版本中，管理單位只能包含使用者和群組。

管理單位可讓您授與系統管理員許可權，限制為您所定義之組織的部門、區域或其他區段。 您可以使用系統管理單位，將許可權委派給地區系統管理員，或在細微層級設定原則。 例如，使用者帳戶系統管理員可以更新設定檔資訊、重設密碼，並只在其管理單位指派授權給使用者。

 例如，委派給地區支援專家，其限制只能管理他們所支援區域[中的使用者](directory-assign-admin-roles.md#helpdesk-administrator)。

## <a name="deployment-scenario"></a>部署案例

使用管理單位來限制系統管理範圍，對於由任何種類的獨立部門組成的組織而言，非常有用。 假設有一家大型大學的範例，它是由許多自發學校（企業營運、工程小組等等）所組成，每個人都有一個 IT 系統管理員團隊，負責控制存取、管理使用者，以及為學校設定原則。 中央系統管理員可以：

- 建立具有系統管理許可權的角色，僅限商業學校管理單位中的 Azure AD 使用者
- 為企業營運建立管理單位
- 只將商業學校的學生和員工填入管理單位
- 將商業學校 IT 小組加入其範圍內的角色

## <a name="license-requirements"></a>授權需求

使用管理單位時，每個管理單位系統管理員都需要 Azure Active Directory Premium 授權，並 Azure Active Directory Free 管理單位成員的授權。 如需詳細資訊，請參閱[開始使用 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)。

## <a name="manage-administrative-units"></a>管理管理單位

在此預覽版本中，您可以使用 Azure 入口網站、PowerShell Cmdlet 和腳本，或 Microsoft Graph 來管理管理單位。 您可以參考我們的檔以取得詳細資料：

- [建立、移除、填入及新增角色至管理單位](roles-admin-units-manage.md)：完整的作法程式
- 使用[管理單位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)：如何使用 PowerShell 處理管理單位
- [管理單元圖形支援](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)：有關系統管理單位 Microsoft Graph 的詳細檔。

### <a name="planning-your-administrative-units"></a>規劃您的管理單位

系統管理單位可以用來以邏輯方式將 Azure AD 資源分組。 例如，對於 IT 部門分散在全球各地的組織而言，建立定義這些地理界限的系統管理單位可能會有意義。 在多國國家組織具有不同「子組織」的另一個案例中，這是作業中的半自我操作，每個子組織可能會以管理單位來表示。

系統會根據組織的獨特需求，引導您建立管理單位的準則。 管理單位是跨 M365 服務定義結構的常見方式。 我們建議您在考慮使用跨 M365 服務時，準備您的管理單位。 當您可以在系統管理單位下，將通用資源與 M365 產生關聯時，您可以從管理單位取得最大值。

您可以預期在組織中建立系統管理單位，以進行下列階段：

1. 初始採用：您的組織將根據初始準則開始建立管理單位，而系統管理單位數目會隨著準則的調整而增加。
1. 剪除：定義適當的準則之後，將會刪除不再需要的系統管理單位。
1. 穩定：您的組織結構已妥善定義，而且系統管理單位數目不會在短暫的持續時間內大幅變更。

## <a name="currently-supported-scenarios"></a>目前支援的案例

全域管理員或特殊許可權角色管理員可以使用 Azure AD 入口網站來建立系統管理單位、將使用者新增為管理單位的成員，然後將 IT 人員指派給系統管理單位範圍的系統管理員角色。 系統管理單位範圍的系統管理員可以使用 Office 365 入口網站，在其管理單位中進行使用者的基本管理。

此外，您可以將群組新增為管理單位的成員，而管理單位範圍的群組系統管理員可以使用 PowerShell、Microsoft Graph 和 Azure AD 入口網站來管理它們。

下表說明目前對管理單元案例的支援。

### <a name="administrative-unit-management"></a>管理單位管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
建立和刪除管理單位   |    支援    |   支援   |    不支援
個別新增和移除管理單位成員    |   支援    |   支援   |    不支援
使用 .csv 檔案大量新增和移除管理單位成員   |    不支援     |  支援   |    沒有計劃可支援
指派管理單位範圍的系統管理員  |     支援    |   支援    |   不支援
根據屬性動態新增和移除 AU 成員 | 不支援 | 不支援 | 不支援

### <a name="user-management"></a>使用者管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
系統管理單位範圍的使用者屬性、密碼、授權管理   |    支援     |  支援   |   支援
使用者登入的系統管理單位範圍封鎖和解除封鎖    |   支援   |    支援   |    支援
系統管理單位範圍的使用者 MFA 認證管理   |    支援   |   支援   |   不支援

### <a name="group-management"></a>群組管理

權限 |   MS Graph/PowerShell   | Azure AD 入口網站 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
群組屬性和成員的系統管理單位範圍管理     |  支援   |    支援    |  不支援
系統管理單位範圍的群組授權管理   |    支援  |    支援   |   不支援

> [!NOTE]
>
> 具有系統管理單位範圍的系統管理員無法管理動態群組成員資格規則。

系統管理單位只會將範圍套用至管理許可權。 它們不會防止成員或系統管理員使用其[預設使用者](../fundamentals/users-default-permissions.md)權力來流覽其他使用者、群組或管理單位以外的資源。 在 Office 365 入口網站中，會篩選出已設定範圍之系統管理員管理單位以外的使用者，但您可以在 Azure AD 入口網站、PowerShell 和其他 Microsoft 服務中流覽其他使用者。

## <a name="next-steps"></a>後續步驟

- [管理 au](roles-admin-units-manage.md)
- [在 au 中管理使用者](roles-admin-units-add-manage-users.md)
- [在 au 中管理群組](roles-admin-units-add-manage-groups.md)
- [將限域角色指派給 AU](roles-admin-units-assign-roles.md)