---
title: 重設使用者的密碼 - Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory 重設使用者密碼的相關指引。
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "69032669"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>使用 Azure Active Directory 重設使用者的密碼

身為系統管理員，您可以在使用者忘記密碼、使用者被鎖定而無法進入裝置，或是使用者從未收到密碼的情況下重設該使用者的密碼。

>[!Note]
>除非您的 Azure AD 租用戶是使用者的主目錄，否則您無法重設其密碼。 這表示如果使用者正在使用另一個組織的帳戶、Microsoft 帳戶或 Google 帳戶來登入您的組織，您將無法重設其密碼。<br><br>如果使用者的授權來源為 Windows Server Active Directory，您只能在已開啟密碼回寫功能時重設密碼。<br><br>如果使用者的授權來源為外部 Azure AD，您將無法重設密碼。 只有使用者或外部 Azure AD 的系統管理員才能重設密碼。

>[!Note]
>如果您不是系統管理員，且正在尋找如何重設自己公司或學校密碼的相關指示，請參閱[重設您的工作密碼或學校密碼](../user-help/active-directory-passwords-update-your-own-password.md)。

## <a name="to-reset-a-password"></a>重設密碼

1. 以使用者系統管理員或密碼管理員身分登入[Azure 入口網站](https://portal.azure.com/)。 如需可用角色的詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. 依序選取 [Azure Active Directory]**** 和 [使用者]****，搜尋並選取需要重設的使用者，然後選取 [重設密碼]****。

    [Alain Charon - 設定檔]**** 頁面隨即出現，其中包含 [重設密碼]**** 選項。

    ![使用者的設定檔頁面，已醒目提示 [重設密碼] 選項](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. 在 [重設密碼]**** 頁面中，選取 [重設密碼]****。

    > [!Note]
    > 使用 Azure Active Directory 時，會自動產生使用者的暫時密碼。 使用內部部署 Active Directory 時，您會建立使用者的密碼。

4. 複製密碼並將它提供給使用者。 使用者必須在下次登入過程中變更密碼。

    >[!Note]
    >暫時密碼永不過期。 下次使用者登入時，密碼仍然有效，而不論產生暫時密碼之後經過多少時間。

## <a name="next-steps"></a>後續步驟

重設使用者的密碼之後，您可以執行下列基本程序：

- [新增或刪除使用者](add-users-azure-active-directory.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [建立基本群組並新增成員](active-directory-groups-create-azure-portal.md)

或者，您也可以執行複雜的使用者案例，例如指派委派、使用原則及共用使用者帳戶。 如需其他可用動作的詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。
