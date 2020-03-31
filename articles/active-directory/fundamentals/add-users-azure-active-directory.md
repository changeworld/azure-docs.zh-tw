---
title: 新增或刪除使用者 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 新增使用者或刪除現有使用者的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262109"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 新增或刪除使用者

添加新使用者或從 Azure 活動目錄 （Azure AD） 組織中刪除現有使用者。 要添加或刪除使用者，您必須是使用者管理員或全域管理員。

## <a name="add-a-new-user"></a>新增使用者

您可以使用 Azure Active Directory 入口網站建立新使用者。

要添加新使用者，請按照以下步驟操作：

1. 以組織的使用者管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。

1. 從任何頁面搜索並選擇*Azure 活動目錄*。

1. 選擇 **"使用者**"，然後選擇 **"新使用者**"。

    ![通過使用者添加使用者 - Azure AD 中的所有使用者](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. 在 **"使用者"** 頁上，輸入此使用者的資訊：

   - [名稱]****。 必要。 新使用者的姓氏與名字。 例如，*瑪麗·派克*。

   - [使用者名稱]****。 必要。 新使用者的使用者名稱。 例如： `mary@contoso.com` 。

     使用者名的域部分必須使用初始預設功能變數名稱、*\<功能變數名稱>.onmicrosoft.com*或自訂功能變數名稱（如*contoso.com*。 有關如何創建自訂功能變數名稱的詳細資訊，請參閱[使用 Azure 活動目錄門戶添加自訂功能變數名稱](add-custom-domain.md)。

   - **組**。 (選擇性) 您可以將使用者新增到一或多個現有的群組。 您也可以稍後再將使用者新增到群組。 有關將使用者添加到組的詳細資訊，請參閱[創建基本組並使用 Azure 活動目錄添加成員](active-directory-groups-create-azure-portal.md)。

   - **目錄角色**：如果需要使用者的 Azure AD 管理許可權，則可以將它們添加到 Azure AD 角色。 您可以將使用者指定為全域管理員或 Azure AD 中的一個或多個有限管理員角色。 如需有關指派角色的詳細資訊，請參閱[如何將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)。

   - **作業資訊**：您可以在此處添加有關使用者的詳細資訊，或稍後執行此操作。 如需新增使用者資訊的詳細資訊，請參閱[如何新增或變更使用者設定檔資訊](active-directory-users-profile-azure-portal.md)。

1. 複製密碼框中提供的自動生成的**密碼**。 您需要首次向使用者提供此密碼才能登錄。

1. 選取 [建立]****。

使用者將創建並添加到 Azure AD 組織。

## <a name="add-a-new-guest-user"></a>添加新來賓使用者

您還可以通過從 **"新建使用者"** 頁面選擇 **"邀請使用者"** 來邀請新來賓使用者與您的組織協作。 如果組織的外部協作設置配置為允許您邀請來賓，則使用者將通過電子郵件發送他們必須接受的邀請才能開始協作。 有關邀請 B2B 協作使用者的詳細資訊，請參閱[邀請 B2B 使用者加入 Azure 活動目錄](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>添加消費者使用者

在某些情況下，可能需要在 Azure 活動目錄 B2C （Azure AD B2C） 目錄中手動創建消費者帳戶。 有關創建消費者帳戶的詳細資訊，請參閱在[Azure AD B2C 中創建和刪除消費者使用者](../../active-directory-b2c/manage-users-portal.md)。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合式環境內新增使用者

若您的環境同時具有 Azure Active Directory (雲端) 與 Windows Server Active Directory (內部部署)，您可以透過同步現有的使用者帳戶資料來新增使用者。 如需有關混合式環境與使用者的詳細資訊，請參閱[整合您的內部部署目錄與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>刪除使用者

您可以使用 Azure Active Directory 入口網站刪除現有的使用者。

要刪除使用者，請按照以下步驟操作：

1. 使用組織的使用者管理員帳戶登錄到[Azure 門戶](https://portal.azure.com/)。

1. 從任何頁面搜索並選擇*Azure 活動目錄*。

1. 搜索並選擇要從 Azure AD 租戶中刪除的使用者。 例如，_瑪麗·派克_。

1. 選取 [刪除使用者]****。

    ![使用者 - 已反白顯示 [刪除使用者] 的 [所有使用者] 頁面](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

使用者會被刪除，而且再也不會出現在 [使用者 - 所有使用者]**** 頁面上。 在接下來的 30 天，您可以在 [刪除的使用者]**** 頁面上看到該使用者，而且在此期間內可將該使用者還原。 有關還原使用者的詳細資訊，請參閱[使用 Azure 活動目錄 還原或刪除最近刪除的使用者](active-directory-users-restore.md)。

刪除使用者後，使用者使用的任何許可證都將提供給其他使用者。

>[!Note]
>您必須使用 Windows 伺服器活動目錄來更新其授權源為 Windows Server 活動目錄的使用者的身份、聯繫資訊或作業資訊。 完成更新之後，您必須等候下一次同步處理循環完成，才能看到您所做的變更。

## <a name="next-steps"></a>後續步驟

添加使用者後，可以執行以下基本過程：

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [建立基本群組並新增成員](active-directory-groups-create-azure-portal.md)

- [使用動態群組與使用者](../users-groups-roles/groups-create-rule.md)

或者，您可以執行其他使用者管理工作，例如[從其他目錄添加來賓使用者](../b2b/what-is-b2b.md)或[還原已刪除的使用者](active-directory-users-restore.md)。 如需其他可用動作的詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。
