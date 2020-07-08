---
title: 變更使用者和群組的授權方案-Azure AD |Microsoft Docs
description: 如何使用 Azure Active Directory 中的群組授權，將群組內的使用者遷移至不同的服務方案
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 07/07/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c0d1efa83af53804be89a9e86f4cafd5bc0ae0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057750"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>在 Azure Active Directory 中變更使用者或群組的授權指派

本文說明如何在 Azure Active Directory （Azure AD）中的服務授權方案之間移動使用者和群組。 目標 Azure AD 的方法是確保在授權變更期間不會遺失任何服務或資料。 使用者應該在服務之間順暢地切換。 本文中的授權方案指派步驟說明如何將 Office 365 E1 上的使用者或群組變更為 Office 365 E3，但這些步驟適用于所有的授權方案。 當您更新使用者或群組的授權指派時，授權指派會移除，而新的指派會同時進行，讓使用者不會在授權變更期間失去其服務的存取權，或查看方案之間的授權衝突。

## <a name="before-you-begin"></a>開始之前

在您更新授權指派之前，請務必確認所有要更新的使用者或群組的特定假設都成立。 如果群組中的所有使用者的假設都不成立，則可能會有部分的遷移失敗。 因此，部分使用者可能就會無法存取服務或資料。 請確定：

- 使用者具有指派給群組並由使用者繼承且不直接指派的目前授權方案（在此案例中為 Office 365 E1）。

- 您所指派的授權方案有足夠的可用授權。 如果您沒有足夠的授權，某些使用者可能不會被指派新的授權方案。 您可以查看可用授權的數目。

- 使用者沒有其他指派的服務授權與所需的授權衝突，或無法移除目前的授權。 例如，來自工作場所分析或 Project Online 等服務的授權，其相依于其他服務。

- 如果您在內部部署環境中管理群組，並透過 Azure AD Connect 將其同步至 Azure AD，則您可以使用內部部署系統來新增或移除使用者。 可能需要一些時間，變更才會與 Azure AD 同步，以供群組授權挑選。

- 如果您要使用 Azure AD 動態群組成員資格，您可以藉由變更其屬性來新增或移除使用者，但授權指派的更新程式仍維持不變。

## <a name="change-user-license-assignments"></a>變更使用者授權指派

在 [**更新授權指派**] 頁面上，如果您看到某些核取方塊無法使用，則表示無法變更的服務，因為它們是繼承自群組授權。

1. 使用 Azure AD 組織中的授權管理員帳戶登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取 [ **Azure Active Directory**  >  **使用者**]，然後開啟使用者的 [**設定檔**] 頁面。
1. 選取 [授權] ****。
1. 選取 [**指派**] 以編輯使用者或群組的授權指派。 [**指派**] 頁面是您可以在其中解決授權指派衝突的地方。
1. 選取 Office 365 E3 的核取方塊，並確定已選取 [至少指派給使用者的 E1 服務]。
1. 清除 Office 365 E1 的核取方塊。

    ![顯示 Office 365 E1 已清除並已選取 Office 365 E3 的使用者授權指派頁面](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. 選取 [儲存]。

Azure AD 會套用新的授權，並同時移除舊的授權，以提供服務持續性。

## <a name="change-group-license-assignments"></a>變更群組授權指派

1. 使用 Azure AD 組織中的授權管理員帳戶登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取 [ **Azure Active Directory**  >  **群組**]，然後開啟群組的 [**總覽**] 頁面。
1. 選取 [授權] ****。
1. 選取 [**指派**] 命令，以編輯使用者或群組的授權指派。
1. 選取 Office 365 E3 的核取方塊。 若要維持服務的持續性，請確定您選取所有已指派給使用者的 E1 服務。
1. 清除 Office 365 E1 的核取方塊。

    ![在 [使用者或群組授權] 頁面上選取 [指派] 命令](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. 選取 [儲存]。

為了提供服務持續性，Azure AD 會套用新的授權，並同時為群組中的所有使用者移除舊的授權。

## <a name="next-steps"></a>後續步驟

請閱讀下列文章來深入了解透過群組管理授權的其他案例：

- [將授權指派給 Azure Active Directory 中的群組](../users-groups-roles/licensing-groups-assign.md)
- [識別及解決 Azure Active Directory 中群組的授權問題](../users-groups-roles/licensing-groups-resolve-problems.md)
- [如何將個別授權的使用者遷移至 Azure Active Directory 中的群組授權](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory 群組授權其他案例](../users-groups-roles/licensing-group-advanced.md)
- [Azure Active Directory 中群組授權的 PowerShell 範例](../users-groups-roles/licensing-ps-examples.md)
