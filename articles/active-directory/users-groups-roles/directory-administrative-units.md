---
title: 管理單位管理(預覽) - Azure AD |微軟文件
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406479"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure 活動目錄中的管理單位管理(預覽版)

本文介紹 Azure 活動目錄 (Azure AD) 中的管理單位。 管理單位是 Azure AD 資源,可以是其他 Azure AD 資源的容器。 在此預覽版本中,管理單位只能包含使用者和組。

管理單位允許您授予僅限於您定義的部門、區域或其他組織部門的管理員許可權。 可以使用管理單位將許可權委派給區域管理員或在粒度級別設置策略。 例如,使用者帳戶管理員可以僅更新其管理單元中的使用者的配置檔資訊、重置密碼和為其分配許可證。

 例如,將[幫助台管理員](directory-assign-admin-roles.md#helpdesk-administrator)角色委派給區域支持專家,僅限於管理他們支持的區域的使用者。

## <a name="deployment-scenario"></a>部署案例

在由任何類型的獨立部門組成的組織中,使用行政單位限制管理範圍是有用的。 以一所由許多自治學校(商學院、工程學院等)組成的大型大學為例,每所學校都有一支 IT 管理員團隊,負責控制訪問、管理使用者和為其學校設置策略。 中央管理員可以:

- 建立具有管理權限的角色,僅對商學院管理單元中的 Azure AD 使用者
- 為商學院建立管理單位
- 僅使用商學院學生和員工填充管理單元
- 將商學院 IT 團隊加入有具有其範圍的角色

## <a name="license-requirements"></a>授權需求

使用管理單元需要為每個管理單元管理員提供 Azure 活動目錄高級許可證,並且對於管理單位成員的 Azure 活動目錄免費許可證。 有關詳細資訊,請參閱使用[Azure AD 進階版入門](../fundamentals/active-directory-get-started-premium.md)。

## <a name="manage-administrative-units"></a>管理管理單位

在此預覽版本中,可以使用 Azure 門戶、PowerShell cmdlet 和腳本或 Microsoft 圖形管理管理單位。 詳情請參閱我們的文件:

- [建立、刪除、填充角色並將其新增到管理單位](roles-admin-units-manage.md):完成操作過程
- [使用管理單位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0):如何使用 PowerShell 使用管理單位
- [管理單位圖支援](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta):有關管理單元的 Microsoft 圖形的詳細文檔。

### <a name="planning-your-administrative-units"></a>規劃管理單位

管理單位可用於邏輯地對 Azure AD 資源進行分組。 例如,對於 IT 部門分散於全球的組織,創建定義這些地理邊界的管理單位可能有意義。 在另一種情況下,多國組織具有不同的"子組織",在業務中是半自治的,每個子組織可能由一個行政單位代表。

創建行政單位的標準將以組織的獨特要求為指導。 管理單元是定義 M365 服務結構的常見方法。 我們建議您在準備管理單元時,在 M365 服務中使用它們。 當可以在管理單位下跨 M365 關聯公共資源時,可以從管理單位獲取最大值。

您可以期望組織中建立管理單位將經歷以下階段:

1. 初始採用:您的組織將開始根據初始條件創建管理單位,並且隨著條件的細化,管理單位的數量將會增加。
1. 修剪:一旦條件定義明確,將刪除不再需要的管理單位。
1. 穩定:您的組織結構定義良好,行政單位的數量不會在短期內發生重大變化。

## <a name="currently-supported-scenarios"></a>目前支援的機制

全域管理員或特權角色管理員可以使用 Azure AD 門戶創建管理單位,將使用者添加為管理單位的成員,然後將 IT 人員分配給管理單位範圍的管理員角色。 然後,管理單位範圍的管理員可以使用 Office 365 門戶對其管理單元中的用戶進行基本管理。

此外,組可以添加為管理單位的成員,並且管理單位範圍的組管理員可以使用 PowerShell、Microsoft 圖形和 Azure AD 門戶管理它們。

下表描述了對管理單元方案的當前支援。

### <a name="administrative-unit-management"></a>行政單位管理

權限 |   MS 圖形/電源外殼   | Azure AD 門戶 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
建立與移除管理單位   |    支援    |   支援   |    不支援
單獨新增及移除管理單位成員    |   支援    |   支援   |    不支援
使用 .csv 檔案批次新增與移除管理單位成員   |    不支援     |  支援   |    沒有支援計劃
配置管理單位範圍的管理員  |     支援    |   支援    |   不支援
從屬性動態新增與刪除 AU 成員 | 不支援 | 不支援 | 不支援

### <a name="user-management"></a>使用者管理

權限 |   MS 圖形/電源外殼   | Azure AD 門戶 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
管理單位範圍管理使用者屬性、密碼、授權   |    支援     |  支援   |   支援
管理單位範圍的阻止和使用者登錄的取消阻止    |   支援   |    支援   |    支援
管理單位範圍管理使用者 MFA 認證   |    支援   |   支援   |   不支援

### <a name="group-management"></a>群組管理

權限 |   MS 圖形/電源外殼   | Azure AD 門戶 | Microsoft 365 系統管理中心
----------- | ----------------------- | --------------- | -----------------
群組屬性與成員管理單位範圍管理     |  支援   |    支援    |  不支援
集團許可管理單位範圍管理   |    支援  |    支援   |   不支援

> [!NOTE]
>
> 具有管理單位作用域的管理員無法管理動態組成員身份規則。

管理單位僅將作用域應用於管理許可權。 它們不會阻止成員或管理員使用其[默認使用者許可權](../fundamentals/users-default-permissions.md)流覽管理單位以外的其他使用者、組或資源。 在 Office 365 門戶中,將篩選出作用域管理員管理單位以外的使用者,但您可以在 Azure AD 門戶、PowerShell 和其他 Microsoft 服務中流覽其他使用者。

## <a name="next-steps"></a>後續步驟

- [管理 A](roles-admin-units-manage.md)
- [管理 A 的使用者](roles-admin-units-add-manage-users.md)
- [管理 A 中的群組](roles-admin-units-add-manage-groups.md)
- [將作用區角色分配給 AU](roles-admin-units-assign-roles.md)