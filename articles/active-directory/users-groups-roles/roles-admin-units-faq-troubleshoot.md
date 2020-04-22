---
title: 管理單位故障排除和常見問題解答 - Azure 活動目錄 |微軟文件
description: 調查管理單位以授予 Azure 活動目錄中具有受限作用域的許可權。
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
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684846"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 管理單元:故障排除和常見問題解答

要在 Azure 活動目錄 (Azure AD) 中進行更精細的管理控制,可以將使用者分配給範圍為一個或多個管理單位 (A) 的 Azure AD 角色。 有關常見工作的範例 PowerShell 文稿,請參閱[使用管理單位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)。

## <a name="frequently-asked-questions"></a>常見問題集

**問:為什麼無法創建管理單位?**

**答:** 只有*全域管理員*或*特權角色管理員*才能在 Azure AD 中創建管理單元。 檢查以確保為試著建立管理單元的使用者配置*全域管理員*或*特權角色管理員角色角色*。

**問:我向管理單位添加了一個組。為什麼組成員仍然不在那裡出現?**

**答:** 將組添加到管理單位時,不會導致將組的所有成員添加到管理單位。 用戶必須直接分配給管理單位。

**問:我剛剛添加(或刪除)管理單位的成員。為什麼成員未在使用者介面中顯示(或仍然顯示)?**

**答:** 有時,處理行政單位的一個或多個成員的添加或刪除可能需要幾分鐘時間才能反映在**行政單位**頁面上。 或者,您可以直接轉到關聯的資源的屬性,並查看操作是否已完成。 有關 A 的使用者和群組的詳細資訊,請參閱[列出使用者和](roles-admin-units-add-manage-users.md)[群組管理單位清單和列出群組管理單位](roles-admin-units-add-manage-groups.md)。

**問:我是管理單位的委派密碼管理員。為什麼我無法重置特定使用者的密碼?**

**答:** 作為管理單位的管理員,您只能為分配給管理單元的使用者重置密碼。 確保密碼重置失敗的使用者屬於已分配您管理單元。 如果用戶屬於同一管理單位,但仍無法重置其密碼,請檢查分配給該使用者的角色。 

為了防止特權提升,管理單位範圍管理員無法重置分配給具有組織範圍的角色的用戶的密碼。

**問:為什麼需要行政單位?我們難道不能使用安全組來定義作用域嗎?**

**答:** 安全組具有現有用途和授權模型。 例如,*使用者管理員*可以管理 Azure AD 組織中所有安全組的成員身份。 該角色可能使用組來管理對應用程式(如 Salesforce)的訪問。 *使用者管理員*不應能夠管理委派模型本身,如果安全組擴展到支援"資源分組"方案,則會產生此結果。 管理單位(如 Windows Server 活動目錄中的組織單位)旨在提供一種對各種目錄物件的範圍管理的範圍的方法。 安全組本身可以是資源作用域的成員。 使用安全組定義管理員可以管理的安全組集可能會變得令人困惑。

**問:將組添加到管理單位意味著什麼?**

**答:** 將組添加到管理單位會將組本身引入任何使用者管理員的管理範圍,這些*使用者管理員*也屬於該管理單位的範圍。 管理單位的使用者管理員可以管理組本身的名稱和成員身份。 它不授予*管理單位的使用者管理員*管理組用戶的許可權(例如,重置其密碼)。 要授予*使用者管理員*管理使用者的能力,用戶必須是管理單位的直接成員。

**問:資源(使用者或組)是否可以是多個管理單位的成員?**

**答:** 可以,資源可以是多個管理單位的成員。 資源可以由對資源具有許可權的所有組織範圍和管理單位範圍的管理員進行管理。

**問:B2C 組織中有可用的管理單位嗎?**

**答:** 否,B2C 組織不提供管理單位。

**問:支援嵌套管理單元嗎?**

**答:** 否,不支援嵌套管理單位。

**問:PowerShell 和圖形 API 中支援管理單元嗎?**

**答:** 是的。 您可以在[PowerShell cmdlet 文件](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview)和[範例文本](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)中找到對管理單元的支援。 

在 Microsoft 圖形中查找對[管理單位資源類型](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit)的支援。

## <a name="next-steps"></a>後續步驟

- [使用管理單位限制角色範圍](directory-administrative-units.md)
- [管理管理單位](roles-admin-units-manage.md)
