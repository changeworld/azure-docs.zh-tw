---
title: 多個租戶交互的特徵 - Azure AD |微軟文件
description: 將 Azure 活動目錄租戶理解為完全獨立的組織
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878114"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>瞭解多個 Azure 活動目錄組織如何互動

在 Azure 活動目錄 (Azure AD) 中,每個租戶都是完全獨立的組織:在邏輯上獨立於您管理的其他 Azure AD 組織的對等體。 組織之間的這種獨立性包括資源獨立性、管理獨立性和同步獨立性。 組織之間沒有父子關係。

## <a name="resource-independence"></a>資源獨立性

* 如果在一個組織中創建或刪除 Azure AD 資源,則對另一個組織中的任何資源沒有影響,但外部使用者除外。
* 如果您在一個組織註冊了其中一個功能變數名稱,則任何其他組織都不能使用它。

## <a name="administrative-independence"></a>系統管理獨立性

如果組織「Contoso」的非管理用戶創建了一個測試組織「測試」,則:

* 默認情況下,創建組織的使用者將作為新組織中的外部使用者添加,並在該組織中分配全域管理員角色。
* 組織「Contoso」的管理員沒有直接管理許可權來組織「測試」,除非「測試」的管理員專門授予他們這些許可權。 但是,"Contoso"的管理員可以控制對組織"測試"的訪問,如果他們控制創建"測試"的使用者帳戶。
* 如果為一個組織中的使用者添加或刪除 Azure AD 角色,則更改不會影響在任何其他 Azure AD 組織中分配該使用者的角色。

## <a name="synchronization-independence"></a>同步處理獨立性

您可以獨立設定每個 Azure AD 組織,以便從以下任一實例同步資料:

* Azure AD Connect 工具：可與單一 AD 樹系同步處理資料。
* Azure Active Directory Connector for Forefront Identity Manager，以與一或多個內部部署樹系和 (或) 非 Azure AD 資料來源同步處理資料。

## <a name="add-an-azure-ad-organization"></a>新增 Azure AD 組織

要在 Azure 門戶中添加 Azure AD 組織,請使用 Azure AD 全域管理員的帳戶登錄到[Azure 門戶](https://portal.azure.com),然後選擇 **"新建**"。

> [!NOTE]
> 與其他 Azure 資源不同,Azure AD 組織不是 Azure 訂閱的子資源。 如果 Azure 訂閱已取消或過期,您仍可以使用 Azure PowerShell、Microsoft 圖形 API 或 Microsoft 365 管理中心訪問 Azure AD 組織的數據。 您還可以[將另一個訂閱與組織相關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>後續步驟

有關 Azure AD 許可注意事項和最佳實踐,請參閱[什麼是 Azure 活動目錄許可?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
