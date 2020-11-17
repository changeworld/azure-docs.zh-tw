---
title: 設定自助群組管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中建立及管理安全性群組或 Microsoft 365 群組，並要求安全性群組或 Microsoft 365 群組成員資格
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6425809cfa6dde20b8ef2b1f6d4cfaf04cbef6e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647081"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>在 Azure Active Directory 中設定自助群組管理 

您可以讓使用者在 Azure Active Directory (Azure AD) 中，建立及管理自己的安全性群組或 Microsoft 365 群組。 群組擁有者可以核准或拒絕成員資格要求，也可以委派群組成員資格的控制權。 自助群組管理功能不適用於具備郵件功能的安全性群組或通訊群組清單。

## <a name="self-service-group-membership-defaults"></a>自助群組成員資格預設值

在 Azure 入口網站中建立安全性群組或使用 Azure AD PowerShell 時，只有群組的擁有者可以更新成員資格。 無論是擁有者核准或自動核准，都可以在 [存取面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) 和所有 Microsoft 365 群組中由自助服務所建立的安全性群組加入所有使用者。 在 [存取面板] 中，您可以在建立群組時變更成員資格選項。

建立的群組 | 安全性群組預設行為 | Microsoft 365 群組預設行為
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | 只有擁有者可以新增成員<br>可在存取面板中顯示但無法加入 | 開啟以加入所有使用者
[Azure 入口網站](https://portal.azure.com) | 只有擁有者可以新增成員<br>可在存取面板中顯示但無法加入<br>建立群組時，不會自動指派擁有者 | 開啟以加入所有使用者
[存取面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 開啟以加入所有使用者<br>建立群組時，可以變更成員資格選項 | 開啟以加入所有使用者<br>建立群組時，可以變更成員資格選項

## <a name="self-service-group-management-scenarios"></a>自助群組管理案例

* **委派的群組管理** 例如，管理公司所使用 SaaS 應用程式存取權的系統管理員。 管理這些存取權限會變得很麻煩，所以此系統管理員會要求企業業主建立新的群組。 系統管理員會將應用程式的存取權指派給新的群組，並將所有已存取該應用程式的使用者新增至群組。 接著，企業業主可以新增更多使用者，而且這些使用者會自動佈建到應用程式。 企業業主不需要等待系統管理員管理使用者的存取權。 如果系統管理員將相同的許可權授與不同商務群組中的管理員，該人員也可以管理自己群組成員的存取權。 商務擁有者或管理員都不能同時查看或管理彼此的群組成員資格。 系統管理員仍然可以看到可以存取應用程式的所有使用者，並視需要封鎖存取權。
* **自助式群組管理** 此案例的範例之一，就是兩位使用者都有各自設定的 SharePoint Online 網站。 他們想要讓彼此的團隊可以存取他們的網站。 為了達成此目的，他們可以在 Azure AD 中建立一個群組，並各自在 SharePoint Online 中選取該群組來提供對他們網站的存取權。 當有人想要存取權時，他們會從 [存取面板] 中要求，而且在核准之後，他們就可以自動取得這兩個 SharePoint Online 網站的存取權。 之後，其中一個人會決定存取網站的所有人員也可以存取特定的 SaaS 應用程式。 SaaS 應用程式的系統管理員可以將此應用程式的存取權限新增到 SharePoint Online 網站。 從那時起，任何獲得核准的要求都可以存取兩個 SharePoint Online 網站，也可以存取此 SaaS 應用程式。

## <a name="make-a-group-available-for-user-self-service"></a>提供可供使用者自助服務的群組

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選取 [ **群組**]，然後選取 **[一般** 設定]。
1. 設定 **擁有者可以在存取面板中管理群組成員資格要求** 為 **[是]**。
1. 將 **存取面板中群組的 [限制存取** ] 設定為 [ **否**]。
1. 如果您設定 **使用者可以在 azure 入口網站中建立安全性群組** ，或 **使用者可以在 azure 入口網站中建立 Microsoft 365 群組** 至

    - **是**：您 Azure AD 組織中的所有使用者都可以建立新的安全性群組，並將成員新增至這些群組。 這些新的群組也會顯示在其他所有使用者的 [存取面板] 中。 如果群組上的原則設定允許，其他使用者可以建立要求來加入這些群組
    - **否**：使用者無法建立群組，也無法變更其為擁有者的現有群組。 不過，他們仍然可以管理這些群組的成員資格，以及核准其他使用者加入其群組的要求。

您也可以使用 **可在 Azure 入口網站中將成員指派為群組擁有者的擁有者** ，以對使用者進行自助群組管理，以取得更細微的存取控制。

當使用者可以建立群組時，您組織中的所有使用者都可以建立新的群組，然後以預設的擁有者身分，將成員新增至這些群組。 您無法指定可以建立自己群組的個人。 您只能指定個人讓另一個群組成員成為群組擁有者。

> [!NOTE]
> 需要 Azure Active Directory Premium (P1 或 P2) 授權，使用者才能要求加入安全性群組或 Microsoft 365 群組，以及讓擁有者核准或拒絕成員資格要求。 若沒有 Azure Active Directory Premium 授權，使用者仍然可以在存取面板中管理其群組，但是他們無法建立需要在存取面板中進行擁有者核准的群組，也無法要求加入群組。

## <a name="next-steps"></a>後續步驟

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
