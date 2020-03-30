---
title: 更改使用者和組的許可證計畫 - Azure AD |微軟文檔
description: 如何使用 Azure 活動目錄中的組許可將組內的使用者遷移到不同的服務方案
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025894"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>更改 Azure 活動目錄中的使用者或組的許可證分配

本文介紹如何在 Azure 活動目錄 （Azure AD） 中的服務許可證計畫之間移動使用者和組。 Azure AD 的方法的目標是確保在許可證更改期間不會丟失服務或資料。 使用者應在服務之間無縫切換。 本文中的許可證計畫分配步驟描述將 Office 365 E1 上的使用者或組更改為 Office 365 E3，但這些步驟適用于所有許可證計畫。 更新使用者或組的許可證分配時，將同時進行許可證分配刪除和新分配，以便使用者不會在許可證更改期間失去對其服務的訪問，也不會看到計畫之間的許可證衝突。

## <a name="before-you-begin"></a>開始之前

在更新許可證分配之前，請務必驗證要更新的所有使用者或組的某些假設是否屬實。 如果組中的所有使用者的假設都不正確，則某些使用者可能會失敗遷移。 因此，部分使用者可能就會無法存取服務或資料。 請確定：

- 使用者具有當前許可證計畫（在本例中為 Office 365 E1），該計畫分配給組並由使用者繼承，並且未直接分配。

- 您有足夠的可用許可證用於您要分配的許可計畫。 如果您沒有足夠的許可證，則可能無法為某些使用者分配新的許可證計畫。 您可以查看可用授權的數目。

- 使用者沒有其他分配的服務許可證，這些許可證可能會與所需的許可證衝突或阻止刪除當前許可證。 例如，來自依賴于其他服務的服務（如工作場所分析或專案連線）的許可證。

- 如果在本地管理組並通過 Azure AD 連接將它們同步到 Azure AD 中，則使用本地系統添加或刪除使用者。 更改與 Azure AD 同步可能需要一些時間才能通過組許可進行拾取。

- 如果使用 Azure AD 動態組成員身份，則通過更改使用者的屬性來添加或刪除使用者，但許可證分配的更新過程保持不變。

## <a name="change-user-license-assignments"></a>更改使用授權分配

在 **"更新許可證分配"** 頁上，如果您看到某些核取方塊不可用，則指示由於服務是從組許可證繼承而無法更改的服務。

1. 使用 Azure AD 組織中的許可管理員帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選擇**Azure 活動目錄** > **使用者**，然後打開使用者的**設定檔**頁。
1. 選取 [授權] ****。
1. 選擇 **"分配"** 以編輯使用者或組的許可證分配。 "**分配"** 頁是可以解決許可證分配衝突的位置。
1. 選中 Office 366 E3 的核取方塊，並確保至少選擇分配給使用者的所有 E1 服務。
1. 清除 Office 365 E1 的核取方塊。

    ![顯示 Office 365 E1 已清除且 Office 365 E3 已清除且已選擇的 Office 365 E3 的使用者的許可證分配頁](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. 選取 [儲存]****。

Azure AD 應用新許可證並同時刪除舊許可證以提供服務連續性。

## <a name="change-group-license-assignments"></a>更改組許可證分配

1. 使用 Azure AD 組織中的許可管理員帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
1. 選擇**Azure 活動目錄** > **組**，然後打開組的 **"概述"** 頁。
1. 選取 [授權] ****。
1. 選擇 **"分配**"命令以編輯使用者或組的許可證分配。
1. 選擇 Office 366 E3 的核取方塊。 為了保持服務的連續性，請確保選擇已分配給使用者的所有 E1 服務。
1. 清除 Office 365 E1 的核取方塊。

    ![在使用者或組許可證頁上選擇"分配"命令](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. 選取 [儲存]****。

為了提供服務連續性，Azure AD 應用新許可證，並同時為組中的所有使用者刪除舊許可證。

## <a name="next-steps"></a>後續步驟

請閱讀下列文章來深入了解透過群組管理授權的其他案例：

- [將授權指派給 Azure Active Directory 中的群組](../users-groups-roles/licensing-groups-assign.md)
- [識別及解決 Azure Active Directory 中群組的授權問題](../users-groups-roles/licensing-groups-resolve-problems.md)
- [如何遷移單個許可使用者到 Azure 活動目錄中的分組許可](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure 活動目錄組許可其他方案](../users-groups-roles/licensing-group-advanced.md)
- [Azure 活動目錄中組許可的 PowerShell 示例](../users-groups-roles/licensing-ps-examples.md)
