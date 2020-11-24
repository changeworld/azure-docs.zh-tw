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
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f6d472f52fa0acf791cc9b5998bf2ebf4e5951b
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503117"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Azure Active Directory 中的使用者或群組變更授權指派

本文說明如何在 Azure Active Directory (Azure AD) 的服務授權方案之間移動使用者和群組。 Azure AD 的目的是要確保在授權變更期間不會遺失任何服務或資料。 使用者應該在服務之間順暢地切換。 本文中的「授權方案指派」步驟說明如何將 Office 365 E1 上的使用者或群組變更為 Office 365 E3，但這些步驟適用于所有授權方案。 當您更新使用者或群組的授權指派時，會同時進行授權指派移除和新指派，讓使用者不會在授權變更期間失去其服務的存取權，或查看方案之間的授權衝突。

## <a name="before-you-begin"></a>開始之前

在您更新授權指派之前，請務必確認所有使用者或群組的特定假設都是正確的，才會更新。 如果群組中的所有使用者的假設都不成立，則遷移可能會失敗。 因此，部分使用者可能就會無法存取服務或資料。 請確定：

- 在此情況下，使用者會有目前的授權方案 (，在此案例中為 Office 365 E1) ，指派給群組並由使用者繼承，而不是直接指派。

- 您所指派的授權方案有足夠的可用授權。 如果您沒有足夠的授權，某些使用者可能不會被指派新的授權方案。 您可以查看可用授權的數目。

- 使用者沒有其他指派的服務授權可能與所需的授權衝突，或防止移除目前的授權。 例如，與其他服務相依的服務（例如工作場所分析或 Project Online）的授權。

- 如果您在內部部署環境中管理群組，並透過 Azure AD Connect 將它們同步至 Azure AD，則您可以使用內部部署系統來新增或移除使用者。 變更可能需要一些時間才能與 Azure AD 同步，以供群組授權挑選。

- 如果您使用 Azure AD 動態群組成員資格，您可以藉由變更其屬性來新增或移除使用者，但授權指派的更新程式仍維持不變。

## <a name="change-user-license-assignments"></a>變更使用者授權指派

在 [ **更新授權指派** ] 頁面上，如果您看到某些核取方塊無法使用，它會指出無法變更的服務，因為它們是從群組授權繼承而來的。

1. 使用 Azure AD 組織中的授權系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
1. 選取 [ **Azure Active Directory**  >  **使用者**]，然後開啟使用者的 [**設定檔**] 頁面。
1. 選取 [授權] 。
1. 選取 [ **指派** ] 以編輯使用者或群組的授權指派。 [ **指派** ] 頁面可讓您解決授權指派衝突。
1. 選取 Office 365 E3 的核取方塊，並確認已選取 [已指派給使用者的所有 E1 服務] 至少。
1. 清除 [Office 365 E1] 的核取方塊。

    ![使用者的 [授權指派] 頁面，其中顯示已清除的 Office 365 E1 和已選取的 Office 365 E3](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. 選取 [儲存]。

Azure AD 套用新的授權，並同時移除舊授權以提供服務持續性。

## <a name="change-group-license-assignments"></a>變更群組授權指派

1. 使用 Azure AD 組織中的授權系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
1. 選取 **Azure Active Directory**  >  **群組**]，然後開啟群組的 [**總覽**] 頁面。
1. 選取 [授權] 。
1. 選取 [ **指派** ] 命令以編輯使用者或群組的授權指派。
1. 選取 Office 365 E3 的核取方塊。 若要維護服務的持續性，請務必選取已指派給使用者的所有 E1 服務。
1. 清除 [Office 365 E1] 的核取方塊。

    ![在 [使用者或群組授權] 頁面上選取 [指派] 命令](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. 選取 [儲存]。

為了提供服務持續性，Azure AD 套用新的授權，並同時為群組中的所有使用者移除舊授權。

## <a name="next-steps"></a>後續步驟

請閱讀下列文章來深入了解透過群組管理授權的其他案例：

- [將授權指派給 Azure Active Directory 中的群組](licensing-groups-assign.md)
- [識別及解決 Azure Active Directory 中群組的授權問題](licensing-groups-resolve-problems.md)
- [如何將個別的授權使用者遷移至 Azure Active Directory 中的群組授權](licensing-groups-migrate-users.md)
- [Azure Active Directory 群組授權其他案例](licensing-group-advanced.md)
- [Azure Active Directory 中群組授權的 PowerShell 範例](licensing-ps-examples.md)
