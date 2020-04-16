---
title: 故障排除管理單位和常見問題解答 - Azure 活動目錄 |微軟文件
description: 調查管理單位以在 Azure 活動目錄中具有受限範圍的權限委派
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
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428143"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Azure 活動目錄中管理單位的故障排除和常見問題解答

要在 Azure 活動目錄 (Azure AD) 中進行更精細的管理控制,可以將使用者分配給範圍僅限於一個或多個管理單位 (A) 的 Azure AD 角色。 您可以在 中https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0找到 用於常見任務的範例 PowerShell 文稿。

## <a name="frequently-asked-questions"></a>常見問題集

**問:無法建立管理單位**

**答:** 只有全域管理員或特權角色管理員才能在 Azure AD 中創建管理單元。 檢查是否為嘗試創建管理單元的使用者分配了全域管理員或特權角色管理員角色角色。

**問:我向行政單位添加了一個組,但組的成員仍未出現在行政單位中**

**答:** 將組添加到管理單位時,不會導致將組的所有成員添加到管理單位。 用戶必須直接分配給管理單位。

**問:我剛剛添加/刪除了管理單元的成員,並且它仍然顯示在UI 中**

**答:** 有時,在 **「管理單位**」頁面下,處理增加或刪除一個或多個行政單位成員可能需要幾分鐘時間。 您可以選擇等待幾分鐘,以便它在管理單位下進行反映。 或者,您可以直接轉到關聯的資源的屬性,並查看操作是否已完成。 有關 A 的使用者和群組的詳細資訊,請參閱[列出使用者和](roles-admin-units-add-manage-users.md)[群組管理單位清單和列出群組管理單位](roles-admin-units-add-manage-groups.md)。

**問:作為管理單元上的委派密碼管理員,我無法重置特定用戶的密碼**

**答:** 通過管理單位分配的管理員,您只能為分配給您的管理單元的使用者重置密碼。 確保密碼重置失敗的用戶屬於已為其分配該角色的管理單位。 如果用戶屬於同一管理單位,但仍無法重置用戶的密碼,請檢查分配給該使用者的角色。 為了防止特權提升,管理單位作用域管理員無法重置分配給具有組織範圍的角色的用戶的密碼。

**問:為什麼需要行政單位?我們難道不能使用安全組來定義作用域嗎?**

**答:** 安全組具有現有用途和授權模型。 例如,使用者管理員可以管理 Azure AD 組織中所有安全組的成員身份,例如使用組來管理對 Salesforce 等應用程式的訪問。 使用者管理員不應能夠管理委派模型本身,如果安全組擴展到支援"資源分組"方案,則會產生這種情況。 管理單位(如 Windows Server 活動目錄中的組織單位)旨在提供一種對各種目錄物件的範圍管理的範圍的方法。 安全組本身可以是資源作用域的成員。 使用安全組定義管理員可以管理的安全組集可能會變得令人困惑。

**問:將組添加到管理單位意味著什麼?**

**答:** 將組添加到管理單位會將組本身引入任何使用者管理員的管理範圍,這些管理員也屬於該管理單位的範圍。 管理單位的使用者管理員可以管理組本身的名稱和成員身份。 它不授予管理單位的使用者管理員管理組使用者的任何許可權(例如,重置其密碼)。 要授予使用者管理員管理使用者的能力,用戶必須是管理單位的直接成員。

**問:資源(使用者或組)是否可以是多個管理單位的成員?**

**答:** 可以,資源可以是多個管理單位的成員。 資源可以由對資源具有許可權的所有組織範圍和管理單位範圍的管理員進行管理。

**問:B2C 組織中有可用的管理單位嗎?**

**答:** 否,B2C 組織不提供管理單位。

**問:支援嵌套管理單元嗎?**

**答:** 不支援嵌套管理單位。

**問:PowerShell 和圖形 API 中支援管理單元嗎?**

**答:** 是的。 管理單位支援在[PowerShell cmdlet 文件](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview)與[範例文稿](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)中,並且支援在 Microsoft 圖形中[用於管理單位資源類型](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit)。

## <a name="next-steps"></a>後續步驟

- [限制角色概述範圍的管理單位](directory-administrative-units.md)
- [管理管理單位](roles-admin-units-manage.md)