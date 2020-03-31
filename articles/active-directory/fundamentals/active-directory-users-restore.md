---
title: 還原或永久刪除最近刪除的使用者 - Azure AD
description: 如何使用 Azure Active Directory 檢視可還原的使用者、還原已刪除的使用者，或是永久刪除使用者。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422870"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>使用 Azure Active Directory 將最近刪除的使用者還原或移除
刪除使用者之後，其帳戶會維持在暫時停權狀態 30 天。 在這 30 天期間，可以還原該使用者帳戶及其所有屬性。 30 天期限過後，系統會自動將該使用者永久刪除。

您可以在 Azure 入口網站中使用 Azure Active Directory (Azure AD)，檢視可還原的使用者、還原已刪除的使用者，或永久刪除使用者。

>[!Important]
>您和 Microsoft 客戶支援服務都無法還原已永久刪除的使用者。

## <a name="required-permissions"></a>所需的權限
您必須具備下列其中一個角色，才能還原及永久刪除使用者。

- 全域管理員

- 合作夥伴第 1 層支援

- 合作夥伴第 2 層支援

- 使用者管理員

## <a name="view-your-restorable-users"></a>檢視可還原的使用者
您可以查看刪除 30 天以內的所有使用者。 這些使用者均可還原。

### <a name="to-view-your-restorable-users"></a>若要檢視可還原的使用者
1. 使用組織的全域管理員帳戶登錄到[Azure 門戶](https://portal.azure.com/)。

2. 選取 [Azure Active Directory] 並選取 [使用者]，然後選取 [已刪除的使用者]。************

    檢閱可還原的使用者清單。

    ![[使用者 - 已刪除的使用者] 頁面上顯示仍可還原的使用者](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>還原最近刪除的使用者

從組織中刪除使用者帳戶時，該帳戶處於掛起狀態，並且保留所有相關的組織資訊。 還原使用者時，也會還原此組織資訊。

> [!Note]
> 還原使用者後，即使這些許可證沒有可用的席位，也會還原在刪除時分配給使用者的許可證。 如果當時使用的許可證數量超過您購買的許可證，則您的組織可能會暫時不符合許可證使用要求。

### <a name="to-restore-a-user"></a>若要還原使用者
1. 在 [使用者 - 已刪除的使用者] **** 頁面上，搜尋並選取其中一個可用的使用者。 例如，_瑪麗·派克_。

2. 選取 [還原使用者]****。

    ![[使用者 - 已刪除的使用者] 頁面上顯示反白的 [還原使用者] 選項](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>永久刪除使用者
您可以永久地從組織中刪除使用者，而無需等待 30 天自動刪除。 無論是您、其他系統管理員還是 Microsoft 客戶支援服務，均無法還原已永久刪除的使用者。

>[!Note]
>如果您意外永久刪除使用者，則必須建立新的使用者，並以手動方式輸入所有先前的資訊。 如需如何建立使用者的詳細資訊，請參閱[新增或刪除使用者](add-users-azure-active-directory.md)。

### <a name="to-permanently-delete-a-user"></a>若要永久刪除使用者

1. 在 [使用者 - 已刪除的使用者] **** 頁面上，搜尋並選取其中一個可用的使用者。 例如 _Rae Huff_。

2. 選取 [永久刪除]****。

    ![[使用者 - 已刪除的使用者] 頁面上顯示反白的 [還原使用者] 選項](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>後續步驟
還原或刪除使用者之後，您可以執行下列基本程序：

- [新增或刪除使用者](add-users-azure-active-directory.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [添加來自其他組織的來賓使用者](../b2b/what-is-b2b.md)

有關其他可用使用者管理工作的詳細資訊[，Azure AD 使用者管理文檔](../users-groups-roles/index.yml)。
