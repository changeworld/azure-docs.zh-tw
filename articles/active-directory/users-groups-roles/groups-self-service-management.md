---
title: 設定自助群組管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中建立及管理安全性群組或 Office 365 群組，並要求安全性群組或 Office 365 群組成員資格
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297998"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>在 Azure 活動目錄中設置自助服務組管理 

您可以允許使用者在 Azure 活動目錄 （Azure AD） 中創建和管理自己的安全性群組或 Office 365 組。 組的擁有者可以批准或拒絕成員資格請求，並可以委派對組成員身份的控制。 自助服務組管理功能不適用於啟用郵件的安全性群組或通訊群組清單。

## <a name="self-service-group-membership-defaults"></a>自助服務組成員身份預設值

在 Azure 門戶或使用 Azure AD PowerShell 中創建安全性群組時，只有該組的擁有者可以更新成員資格。 ["Access"面板](https://account.activedirectory.windowsazure.com/r#/joinGroups)中的自助服務創建的安全性群組和所有 Office 365 組可供所有使用者加入，無論是擁有者批准還是自動批准。 在"訪問"面板中，您可以在創建組時更改成員資格選項。

在 | 安全性群組預設行為 | Office 365 組預設行為
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 只有擁有者可以添加成員<br>可見但無法加入訪問面板 | 開放以加入所有使用者
[Azure 門戶](https://portal.azure.com) | 只有擁有者可以添加成員<br>可見但無法加入訪問面板<br>在組創建時不會自動分配擁有者 | 開放以加入所有使用者
[訪問面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 開放以加入所有使用者<br>創建組時可以更改成員資格選項 | 開放以加入所有使用者<br>創建組時可以更改成員資格選項

## <a name="self-service-group-management-scenarios"></a>自助服務組管理方案

* **委派的組管理**例如，管理公司正在使用的 SaaS 應用程式的存取權限的管理員。 管理這些存取權限會變得很麻煩，所以此系統管理員會要求企業業主建立新的群組。 管理員將應用程式的存取權限分配給新組，並將已訪問該應用程式的所有人添加到組中。 接著，企業業主可以新增更多使用者，而且這些使用者會自動佈建到應用程式。 企業業主不需要等待系統管理員管理使用者的存取權。 如果管理員向其他業務組中的經理授予相同的許可權，則該人員還可以管理其自己的組成員的訪問。 企業擁有者和經理都不能查看或管理彼此的組成員身份。 系統管理員仍然可以看到可以存取應用程式的所有使用者，並視需要封鎖存取權。
* **自助服務組管理**此方案的一個示例是兩個使用者，他們都有獨立設置的 SharePoint Online 網站。 他們希望讓彼此的團隊訪問他們的網站。 為了達成此目的，他們可以在 Azure AD 中建立一個群組，並各自在 SharePoint Online 中選取該群組來提供對他們網站的存取權。 當有人想要存取權時，他們會從 [存取面板] 中要求，而且在核准之後，他們就可以自動取得這兩個 SharePoint Online 網站的存取權。 之後，其中一個人會決定存取網站的所有人員也可以存取特定的 SaaS 應用程式。 SaaS 應用程式的系統管理員可以將此應用程式的存取權限新增到 SharePoint Online 網站。 從那時起，任何獲得核准的要求都可以存取兩個 SharePoint Online 網站，也可以存取此 SaaS 應用程式。

## <a name="make-a-group-available-for-user-self-service"></a>提供可供使用者自助服務的群組

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選擇 **"組**"，然後選擇 **"常規**設置"。
1. 集**擁有者可以在"訪問面板"中管理組成員身份請求**。 **Yes**
1. 將**訪問面板中的組限制為****"否**"
1. 如果設置 **"使用者可以在 Azure 門戶中創建安全性群組"，** 或者**使用者可以在 Azure 門戶中創建 Office 365 組**，

    - **是**：允許 Azure AD 組織中的所有使用者創建新的安全性群組並將成員添加到這些組。 這些新的群組也會顯示在其他所有使用者的 [存取面板] 中。 如果組上的策略設置允許，則其他使用者可以創建加入這些組的請求
    - **否**：使用者無法創建組，也不能更改其擁有者的現有組。 不過，他們仍然可以管理這些群組的成員資格，以及核准其他使用者加入其群組的要求。

您還可以使用**可以將成員分配為 Azure 門戶中的組擁有者的擁有者，** 也可以將**成員指定為 Azure 門戶中的組擁有者的擁有者**，以便對使用者的自助服務組管理實現更精細的存取控制。

當使用者可以創建組時，允許組織中的所有使用者創建新組，然後作為預設擁有者，可以將成員添加到這些組。 不能指定可以創建自己的組的個人。 只能指定個人，使另一個組成員成為組擁有者。

## <a name="next-steps"></a>後續步驟

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](groups-settings-cmdlets.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure 活動目錄？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
