---
title: 管理已從條件式存取原則排除的使用者-Azure AD
description: 瞭解如何使用 Azure Active Directory （Azure AD）存取審查來管理已從條件式存取原則中排除的使用者
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91bb5a342eea079b6e9abcf109ad472151d3c13d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144490"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>使用 Azure AD 存取審查來管理從條件式存取原則中排除的使用者

在理想的世界中，所有使用者都會遵循存取原則來保護您組織資源的存取。 不過，有時候會有需要您視為例外狀況的商務案例。 本文會介紹一些可能需要排除的情況範例。 身為 IT 系統管理員，您可以管理這項工作、避免監督原則例外狀況，並提供審計員證明這些例外狀況是使用 Azure Active Directory （Azure AD）存取權審查來定期審查。

>[!NOTE]
> 需要已付費的有效 Azure AD Premium P2、Enterprise Mobility + Security E5 或試用版授權，才能使用 Azure AD 存取權檢閱。 如需詳細資訊，請參閱[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="why-would-you-exclude-users-from-policies"></a>您為何要從原則中排除使用者？

假設身為系統管理員，您決定使用[Azure AD 條件式存取](../conditional-access/overview.md)來要求多重要素驗證（MFA），並限制對特定網路或裝置的驗證要求。 在部署規劃期間，您發現並非所有的使用者都可以符合這些需求。 例如，您可能有從遠端辦公室工作的使用者，而不是內部網路的一部分。 您可能也必須配合使用不受支援的裝置來連線的使用者，同時等待這些裝置被取代。 簡言之，企業會要求這些使用者登入並執行其工作，因此您可以將其從條件式存取原則中排除。

另一個範例是，您可能會在條件式存取中使用[已命名的位置](../conditional-access/location-condition.md)來指定一組您不想讓使用者存取其租使用者的國家/地區和區域。

![條件式存取中的命名位置](./media/conditional-access-exclusion/named-locations.png)

可惜的是，有些使用者可能仍有從這些封鎖的國家/地區登入的有效理由。 例如，使用者可能會出差，而且需要存取公司資源。 在此情況下，封鎖這些國家/地區的條件式存取原則，可以將雲端安全性群組用於來自原則的已排除使用者。 在旅行時需要存取的使用者，可以使用 [Azure AD 自助式群組管理](../users-groups-roles/groups-self-service-management.md)將本身新增到群組。

另一個範例可能是您有條件式存取原則[封鎖了大部分使用者的舊版驗證](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)。 不過，如果您有一些使用者需要使用舊版驗證方法，透過 Office 2010 或 IMAP/SMTP/POP 用戶端來存取您的資源，則您可以從封鎖舊版驗證方法的原則中排除這些使用者。

>[!NOTE]
>Microsoft 強烈建議您在租用戶中封鎖使用舊版通訊協定，以改善安全性狀態。

## <a name="why-are-exclusions-challenging"></a>為何排除項目具有挑戰性？

在 Azure AD 中，您可以將條件式存取原則的範圍設定為一組使用者。 您也可以選取 [Azure AD 角色]、[個別使用者] 或 [來賓] 來設定排除專案。 您應該記住，當設定排除專案時，不能在排除的使用者上強制執行原則意圖。 如果排除專案是使用使用者清單或使用舊版內部部署安全性群組來設定，則您的排除範圍會有所限制。 因此：

- 使用者可能不知道它們已被排除。

- 使用者可以加入安全性群組以略過原則。

- 排除的使用者可能必須先限定排除，但可能不再符合資格。

通常，當您第一次設定排除時，會篩選略過原則的使用者。 經過一段時間後，會有更多使用者加入排除範圍，且清單會擴大。 在某個時間點，您需要檢查清單，並確認每個使用者仍有資格進行排除。 從技術觀點來看，管理排除清單可能相當簡單，但是誰會做出商務決策，以及如何確保它全都可以進行審核？ 不過，如果您使用 Azure AD 群組來設定排除，就可以使用存取審查作為補償控制項來驅動可見度，並減少排除的使用者數目。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>如何在條件式存取原則中建立排除群組

請遵循下列步驟來建立新的 Azure AD 群組，以及不適用該群組的條件式存取原則。

### <a name="create-an-exclusion-group"></a>建立排除群組

1. 登入 Azure 入口網站。

2. 在左側導覽列中，按一下 [Azure Active Directory]****，然後按一下 [群組]****。

3. 在上方功能表中，按一下 [新增群組]**** 以開啟群組窗格。

4. 在 [群組類型]**** 清單中，選取 [安全性]****。 指定名稱和描述。

5. 務必將 [成員資格]**** 類型設定為 [已指派]****。

6. 選取應成為此排除群組成員的使用者，然後按一下 [建立]****。

![Azure Active Directory 中的 [新增群組] 窗格](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>建立排除群組的條件式存取原則

現在您可以建立使用此排除群組的條件式存取原則。

1. 在左側導覽中，按一下 [ **Azure Active Directory** ]，然後按一下 [**條件式存取**] 以開啟 [**原則**] 分頁。

2. 按一下 [新增原則]**** 以開啟 [新增]**** 窗格。

3. 指定名稱。

4. 在 [指派] 底下按一下 [使用者和群組]****。

5. 在 [包含]**** 索引標籤上，選取 [所有使用者]****。

6. 在 [**排除**] 索引標籤上，對 [**使用者和群組**] 新增核取記號，然後按一下 [**選取排除的使用者**]。

7. 選取您所建立的排除群組。

   > [!NOTE] 
   > 最佳做法：建議在測試時從原則中排除至少一個系統管理員帳戶，以確保您不會被鎖定在您的租用戶外。

8. 根據您的組織需求，繼續設定條件式存取原則。

![在條件式存取中選取排除的使用者窗格](./media/conditional-access-exclusion/select-excluded-users.png)
  
讓我們來討論兩個範例，您可以在其中使用存取審查來管理條件式存取原則中的排除專案。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>範例1：從已封鎖的國家/地區存取使用者的存取權審查

假設您有一個條件式存取原則，可封鎖特定國家/地區的存取。 其中包含從原則中排除的群組。 以下是會檢閱群組成員的建議存取權檢閱。

> [!NOTE] 
> 需要全域管理員或使用者系統管理員角色，才能建立存取權審查。

1. 每週會進行審核。

2. 永遠不會結束，以確保您將此排除群組保持在最新狀態。

3. 此群組的所有成員都在檢閱範圍內。

4. 每位使用者都必須自行證明，他們仍然需要來自這些封鎖國家/地區的存取權，因此他們仍然必須是該群組的成員。

5. 如果使用者未回應審核要求，則會自動從群組中移除，而且在傳送至這些國家/地區時，他們將無法再存取租使用者。

6. 啟用電子郵件通知，讓使用者知道存取權審查的開始和完成。

    ![建立存取權審查窗格，例如1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>範例 2：可供使用者透過舊版驗證存取的存取權檢閱

假設您有條件式存取原則，可封鎖使用舊版驗證和舊版用戶端的使用者存取，並包含從原則中排除的群組。 以下是會檢閱群組成員的建議存取權檢閱。

1. 此檢閱必須是週期性檢閱。

2. 群組中的每個人都必須經過檢閱。

3. 您可以將該原則設定為列出商務單位擁有者作為選取的檢閱者。

4. 自動套用結果，並移除並未獲准繼續使用舊版驗證方法的使用者。

5. 這可能有助於啟用建議事項，讓大型群組的檢閱者得以輕鬆地進行其決策。

6. 啟用郵件通知，讓使用者能收到開始和完成存取權檢閱的通知。

    ![建立存取權審查窗格，例如2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>如果您有許多排除群組，因此需要建立多個存取權審查，我們現在在 Microsoft Graph Beta 端點中有一個 API，可讓您以程式設計方式建立及管理它們。 若要開始，請參閱 [Azure AD 存取權檢閱 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root)和[透過 Microsoft Graph 擷取 Azure AD 存取權檢閱的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)。

## <a name="access-review-results-and-audit-logs"></a>存取權檢閱結果和稽核記錄

既然您已備妥所有專案、群組、條件式存取原則和存取審查，就可以監視和追蹤這些評論的結果。

1. 在 Azure 入口網站中，開啟 [**存取評論**] 分頁。

2. 開啟您為了管理排除群組所建立的控制項和程式。

3. 按一下 [結果]****，查看有誰獲准留在清單上，以及誰已遭到移除。

    ![存取審查結果會顯示已核准的人員](./media/conditional-access-exclusion/access-reviews-results.png)

4. 然後按一下 [稽核記錄]**** 以查看在此檢閱期間採取的動作。

    ![存取審查記錄列出動作](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 系統管理員知道管理您原則的排除群組有時是不可避免的。 不過，若要維護這些群組，請在商務擁有者或使用者本身定期進行檢查，然後使用 Azure AD 的存取權審查，輕鬆地進行這些變更的審核。

## <a name="next-steps"></a>後續步驟

- [建立群組或應用程式的存取權審查](create-access-review.md)
- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)
