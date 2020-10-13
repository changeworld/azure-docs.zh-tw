---
title: 關閉非受控 Azure AD 組織中的公司或學校帳戶
description: 如何關閉非受控 Azure Active Directory 中的公司或學校帳戶。
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e03214e2a00a16064763f35f38bdd3ea0e6123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798324"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>關閉非受控 Azure AD 組織中的公司或學校帳戶

如果您是在非受控 Azure Active Directory 中的使用者 (Azure AD) 組織，而您不再需要使用該組織的應用程式或維護任何與其相關聯的應用程式，您可以隨時關閉您的帳戶。 未受管理的組織沒有全域管理員。 未受管理組織中的使用者可以自行關閉其帳戶，而不需要洽詢系統管理員。

非受控組織中的使用者通常會在自助註冊期間建立。 例如，組織中的資訊工作者會註冊免費服務。 如需自助式註冊的詳細資訊，請參閱 [什麼是 Azure Active Directory 的自助式註冊？](directory-self-service-signup.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>開始之前

您應該先確認下列專案，才能關閉帳戶：

* 請確定您是非受控 Azure AD 組織的使用者。 如果您屬於受管理的組織，則無法關閉您的帳戶。 如果您屬於受管理的組織，而且想要關閉您的帳戶，您必須洽詢系統管理員。 如需如何判斷您是否屬於非受控組織的詳細資訊，請參閱 [從非受控租使用者刪除使用者](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)。

* 儲存您想要保留的任何資料。 如需有關如何提交匯出要求的詳細資訊，請參閱 [存取和匯出非受控租使用者的系統產生記錄](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)。

> [!WARNING]
> 關閉您的帳戶是無法復原的。 當您關閉帳戶時，將會移除所有個人資料。 您將無法再存取您的帳戶和與您的帳戶相關聯的資料。

## <a name="close-your-account"></a>關閉您的帳戶

若要關閉未受管理的工作或學校帳戶，請遵循下列步驟：

1. 使用您想要關閉的帳戶登入，以 [關閉您的帳戶](https://go.microsoft.com/fwlink/?linkid=873123)。

1. 在 [ **我的資料要求**] 上，選取 [ **關閉帳戶**]。

    ![我的資料要求-關閉帳戶](./media/users-close-account/close-account.png)

1. 檢查確認訊息，然後選取 **[是]**。

    ![我的資料要求-確認關閉](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>接下來的步驟

- [什麼是 Azure Active Directory 的自助式註冊？](directory-self-service-signup.md)
- [從非受控租用戶刪除使用者](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [存取和匯出非受控租用戶的系統產生記錄檔](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)