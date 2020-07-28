---
title: 多個租用戶互動的特性 - Azure AD | Microsoft Docs
description: 了解 Azure Active Directory 組織的資料獨立性
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1db812fb9c1503771bec0b1795ee8c15bb70014a
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84727699"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>了解多個 Azure Active Directory 組織的互動方式

在 Azure Active Directory (Azure AD) 中，每個 Azure AD 組織都完全獨立：邏輯上獨立於您所管理之其他 Azure AD 組織的同儕節點。 組織之間的此種獨立性包括資源獨立性、系統管理獨立性，以及同步處理獨立性。 組織之間沒有任何父子關聯性。

## <a name="resource-independence"></a>資源獨立性

* 如果您在某個組織中建立或刪除 Azure AD 資源，則不會影響另一個組織中的任何資源 (但外部使用者有部分例外狀況)。
* 如果您向某個組織註冊您的其中一個網域名稱，則任何其他組織都無法使用該名稱。

## <a name="administrative-independence"></a>系統管理獨立性

如果 'Contoso' 組織的非系統管理使用者建立 'Test' 測試組織，則：

* 系統預設會將建立組織的使用者新增為該新組織的外部使用者，並將該組織中的全域管理員角色指派給他。
* 'Contoso' 組織的系統管理員並沒有 'Test' 組織的直接系統管理權限，除非 'Test' 的系統管理員明確地授與他們這些權限。 不過，'Contoso' 的系統管理員如果可控制建立 'Test' 的使用者帳戶，便可控制對 'Test' 組織的存取權。
* 如果您在某個組織中新增或移除使用者的 Azure AD 角色，則變更不會影響使用者在任何其他 Azure AD 組織中被指派的角色。

## <a name="synchronization-independence"></a>同步處理獨立性

您可以單獨設定每個 Azure AD 組織，以從下列任一工具的單一執行個體同步處理資料：

* Azure AD Connect 工具：可與單一 AD 樹系同步處理資料。
* Azure Active Directory Connector for Forefront Identity Manager，以與一或多個內部部署樹系和 (或) 非 Azure AD 資料來源同步處理資料。

## <a name="add-an-azure-ad-organization"></a>新增 Azure AD 組織

若要在 Azure 入口網站中新增 Azure AD 組織，請使用 Azure AD 全域管理員的帳戶登入[Azure 入口網站](https://portal.azure.com)，然後選取 [新增]****。

> [!NOTE]
> 與其他 Azure 資源不同，Azure AD 組織並非 Azure 訂用帳戶的子資源。 如果您的 Azure 訂用帳戶被取消或到期，您仍然可以使用 Azure PowerShell、Microsoft Graph API 或 Microsoft 365 系統管理中心來存取 Azure AD 組織的資料。 您也可以[將另一個訂用帳戶與組織建立關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>後續步驟

如需 Azure AD 授權考量和最佳做法，請參閱[什麼是 Azure Active Directory 授權？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)。
