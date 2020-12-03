---
title: 使用直接授權將使用者新增至群組授權-Azure AD |Microsoft Docs
description: 如何使用 Azure Active Directory 從個別使用者授權遷移至以群組為基礎的授權
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546500"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>如何將具有個別授權的使用者遷移至群組以進行授權

您可能會透過直接指派將現有的授權部署給組織中的使用者。亦即，使用 PowerShell 腳本或其他工具來指派個別使用者授權。 在您開始使用以群組為基礎的授權來管理組織中的授權之前，您可以使用此遷移計畫，透過以群組為基礎的授權順暢地取代現有的解決方案。

要謹記在心最重要的事情是，您應該避免移轉到以群組為基礎的授權會導致使用者暫時遺失其目前獲得指派授權的情況。 應避免可能造成授權移除的任何處理，以便移除使用者失去服務及其資料存取權的風險。

## <a name="recommended-migration-process"></a>建議的移轉程序

1. 您有現有的自動化 (例如，PowerShell) 管理使用者的授權指派及移除。 保留它的執行原樣。

1. 建立新的授權群組 (或決定要使用哪個現有群組)，並確定所有必要的使用者新增為成員。

1. 將必要的授權指派給這些群組；您的目標應該是反映您現有的自動化 (例如，PowerShell) 的相同授權狀態會套用至這些使用者。

1. 確認授權已套用至這些群組中的所有使用者。 此應用程式可以透過檢查每個群組上的處理狀態以及檢查稽核記錄來完成。

   - 您可以特別檢查個別使用者，方法是查看他們的授權詳細資料。 您會看到他們具有從群組「直接」和「繼承」指派的相同授權。

   - 您可以執行 PowerShell 指令碼，以[確認授權指派給使用者的方式](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)。

   - 當相同的產品授權同時直接和透過群組指派給使用者時，只能有一個授權由使用者取用。 因此不需要額外授權即可執行移轉。

1. 確認沒有授權指派失敗，方法是檢查每個群組中是否有使用者處於錯誤狀態。 如需詳細資訊，請參閱[識別及解決群組的授權問題](licensing-groups-resolve-problems.md)。

請考慮移除原始的直接指派。 建議您逐步進行，並先監視使用者子集上的結果。 如果您可以離開使用者的原始直接指派，但是當使用者離開其授權群組時，他們會保留直接指派的授權，而這可能不是您想要的。

## <a name="an-example"></a>範例

組織有 1,000 位使用者。 所有使用者都需要 Office 365 Enterprise E3 授權。 組織目前有內部部署執行的 PowerShell 指令碼，並在使用者就職和離職時新增及移除授權。 不過，組織想要將指令碼取代為群組授權，這樣授權就可以自動由 Azure AD 管理。

以下是移轉程序可能的情況︰

1. 使用 Azure 入口網站，在 Azure AD 中將 Office 365 E3 授權指派給 [ **所有使用者** ] 群組。

1. 確認已完成所有使用者的授權指派。 移至群組的 [總覽] 頁面，選取 [ **授權**]，然後檢查 [ **授權** ] 分頁頂端的處理狀態。

   - 尋找「最新的授權變更已套用至所有使用者」以確認處理已完成。

   - 在最上層尋找任何使用者的授權可能尚未成功指派的相關通知。 我們對於某些使用者是否已用盡授權？ 某些使用者是否有衝突的授權方案，使其無法繼承群組授權？

1. 特別檢查某些使用者，以確認其套用直接和群組授權。 移至使用者的 [設定檔] 頁面，選取 [ **授權**]，然後檢查授權狀態。

   - 這是移轉期間預期的使用者狀態︰

      ![在遷移期間預期的使用者狀態](./media/licensing-groups-migrate-users/expected-user-state.png)

     這可確認使用者同時具有直接和繼承的授權。 我們會看到已指派 Office 365 E3。

   - 選取每個授權以查看已啟用的服務。 若要確認直接和群組授權為使用者啟用完全相同的服務，請選取 [ **指派**]。

1. 在確認直接和群組授權是對等的之後，您可以開始從使用者移除直接授權。 您可以藉由在入口網站中移除個別使用者的直接授權來進行測試，然後再執行自動化指令碼，將它們大量移除。 以下是透過入口網站移除直接授權的相同使用者範例。 請注意，授權狀態會維持不變，但是我們不會再看見直接指派。

   ![確認已移除直接授權](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>後續步驟

深入瞭解群組授權管理的其他案例：

- [什麼是 Azure Active Directory 中以群組為基礎的授權？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [將授權指派給 Azure Active Directory 中的群組](licensing-groups-assign.md)
- [識別及解決 Azure Active Directory 中群組的授權問題](licensing-groups-resolve-problems.md)
- [如何使用 Azure Active Directory 中的群組型授權在產品授權之間移轉使用者](licensing-groups-change-licenses.md)
- [Azure Active Directory 群組型授權其他案例 (英文)](licensing-group-advanced.md)
- [Azure Active Directory 群組型授權的 PowerShell 範例](licensing-ps-examples.md)
