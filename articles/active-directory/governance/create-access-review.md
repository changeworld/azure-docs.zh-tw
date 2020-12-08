---
title: 建立群組 & 應用程式的存取權檢查-Azure AD
description: 瞭解如何在 Azure Active Directory 存取權評論中，建立群組成員或應用程式存取權的存取權審核。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778529"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取權評論中建立群組和應用程式的存取權審核

員工和來賓對於群組和應用程式的存取權會隨著時間而變更。 為了降低過時存取權指派的相關風險，系統管理員可以使用 Azure Active Directory (Azure AD)，建立群組成員或應用程式存取的存取權檢閱。 如果您需要定期檢閱存取權，您也可以建立週期性存取權檢閱。 如需關於這些案例的詳細資訊，請參閱[管理使用者存取權](manage-user-access-with-access-reviews.md)和[管理來賓存取權](manage-guest-access-with-access-reviews.md)。

您可以觀看影片，瞭解如何啟用存取權審核：

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

本文說明如何針對群組成員或應用程式存取建立一或多個存取權審核。

## <a name="prerequisites"></a>先決條件

- Azure AD Premium P2
- 全域管理員或使用者管理員

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>建立一或多個存取權檢閱

1. 登入 Azure 入口網站，然後開啟 [身分 [識別管理] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

2. 在左側功能表中，按一下 [ **存取審核**]。

3. 按一下 [新增存取權檢閱]，以建立新的存取權檢閱。

    ![身分識別管理中的 [存取審核] 窗格](./media/create-access-review/access-reviews.png)

4. 在 [ **步驟1：選取要檢查的內容** ] 中，選取您要檢查的資源。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/create-access-review/select-what-review.png)

5. 如果您在步驟1中選取 [ **小組 + 群組** ]，則在步驟2中有兩個選項。
   - **具有來賓使用者的所有 Microsoft 365 群組。** 如果您想要在組織中所有 Microsoft 小組和 M365 群組的所有來賓使用者上建立週期性評論，請選取此選項。 您可以按一下 [選取要排除的群組 () ，以選擇排除特定群組。
   - **選取 [小組 + 群組]。** 如果您想要指定一組有限的小組和/或群組來檢查，請選取此選項。 按一下這個選項之後，您會看到要從中挑選的群組清單。

     ![小組和群組](./media/create-access-review/teams-groups.png)

     ![使用者介面中選擇的小組和群組](./media/create-access-review/teams-groups-detailed.png)

6. 如果您已在步驟1中選取 **應用程式** ，則可以在步驟2中選取一個或多個應用程式。

    >[!NOTE]
    > 選取多個群組和/或應用程式會導致建立多個存取權審核。 例如，如果您選取5個要審核的群組，則會產生5個不同的存取權評論

   ![如果您選擇應用程式而不是群組，則會顯示介面](./media/create-access-review/select-application-detailed.png)

7. 接下來，您可以在步驟3中選取評論的範圍。 您的選項如下
   - **僅限來賓使用者。** 選取此選項會將存取權審核限制在您目錄中的 Azure AD B2B 來賓使用者。
   - **每個人 都。** 選取此選項會將存取權檢查的範圍設為與資源相關聯的所有使用者物件。

    >[!NOTE]
    > 如果您在步驟2中選取了具有來賓使用者的所有 Microsoft 365 群組，則唯一的選項是在步驟3中檢查來賓使用者

8. 按一下 [下一步]：評論
9. 在 [ **選取審核者** ] 區段中，選取一或多個人員來執行存取權審核。 您可以選擇：
    - **群組擁有者 (s)** 只有在對小組或群組執行評論時才能使用 () 
    - **選取的使用者 (s) 或群組 (s)**
    - **使用者評論自己的存取權**
    - **(預覽版) 的使用者管理員。**
    如果您選擇其中一個使用者或 **群組擁有** 者 **的管理員**，您也可以選擇指定回溯審核者。 當使用者未在目錄中指定任何管理員，或群組沒有擁有者時，系統會要求回溯審核者進行審核。

    ![新的存取權審核](./media/create-access-review/new-access-review.png)

10. 在 [ **指定週期的檢查** ] 區段中，您可以指定頻率 **，例如每週、每月、每季、每半年、每年**。 然後，您可以指定 **持續時間**，以定義要針對審核者的輸入開啟審核的時間長度。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。 您可能會想要縮短持續時間，以確保先前會套用您的審核者輸入。 接下來，您可以選取 **開始日期** 和 **結束日期**。

    ![選擇應進行審核的頻率](./media/create-access-review/frequency.png)

11. 按一下頁面底部的 [ **下一步：設定]** 按鈕
12. 在 [ **完成時] 設定** 中，您可以指定完成審核之後會發生的情況

    ![建立存取權檢查-完成設定時](./media/create-access-review/upon-completion-settings-new.png)

如果您想要自動移除拒絕的使用者存取權，請將 [自動將結果套用至資源] 以啟用。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]。
使用 [如果審核者未回應] 清單，以指定審核期間內的審核者未審核的使用者會發生什麼事。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

- **無變更** - 使用者的存取權保持不變
- **移除存取權** - 移除使用者的存取權
- **核准存取權** - 核准使用者的存取權
- **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

    ![完成設定選項時](./media/create-access-review/upon-completion-settings-new.png)

使用在拒絕的 **來賓** 使用者上套用的動作，以指定當來賓使用者遭到拒絕時，會發生什麼事。
- 移除使用者的資源成員資格將會移除拒絕的使用者存取群組或正在審核的應用程式，他們仍然可以登入租使用者。
- 封鎖使用者登入30天，然後從租使用者中移除使用者將會封鎖拒絕的使用者登入租使用者，而不論他們是否有其他資源的存取權。 如果發生錯誤，或系統管理員決定重新啟用一個存取權，他們就可以在使用者停用之後的30天內完成這項作業。 如果停用的使用者沒有採取任何動作，則會從租使用者中刪除。

若要深入瞭解移除不再具有您組織資源存取權之來賓使用者的最佳作法，請閱讀標題為 [使用 Azure AD Identity Governance 的文章，以檢查並移除不再具有資源存取權的外部使用者。](access-reviews-external-users.md)

   >[!NOTE]
   >針對超過來賓使用者的評論，無法設定已拒絕來賓使用者適用的動作。 也無法針對 **所有具有來賓使用者的 M365 群組** 進行評論。 若無法設定，則會在拒絕的使用者上使用從資源移除使用者成員資格的預設選項。

13. 在 [ **啟用審核決策** 協助程式] 中，選擇您是否希望審核者在審核過程中收到建議。

    ![啟用決策協助程式選項](./media/create-access-review/helpers.png)

14. 在 [ **Advanced settings** ] 區段中，您可以選擇下列各項
    - 設定 [**啟用**]**所需的理由**，以要求審核者提供核准的原因。
    - 設定 **電子郵件通知** **，讓 Azure AD** 在存取權檢查開始時，將電子郵件通知傳送給審核者，並在審核完成時將電子郵件通知傳送給系統管理員。
    - 將 [提醒] 設為 [啟用]，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。 這些提醒會在評論的持續時間內自行進行。
    - 傳送給審核者之電子郵件的內容會根據審核詳細資料自動產生，例如審核名稱、資源名稱、到期日等。如果您需要一種方式來傳達額外的資訊，例如其他指示或連絡人資訊，您可以在 [ **其他檢閱者的內容** ] 區段中指定這些詳細資料。 您輸入的資訊會包含在傳送給指派審核者的邀請和提醒電子郵件中。 下圖中反白顯示的區段會顯示這項資訊的顯示位置。


      ![評論者的其他內容](./media/create-access-review/additional-content-reviewer.png)

15. 按一下 **[下一步]： [檢查 + 建立** ] 以移至下一個頁面
16. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。
17. 檢查資訊，然後選取 [**建立**]

       ![建立審核畫面](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]。 存取權審核將會出現在清單中，並顯示其狀態的指標。

![存取權評論及其狀態的清單](./media/create-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們顯示如何 [審查群組或應用程式存取權](perform-access-review.md)的指示。 如果您的審查是讓來賓檢查自己的存取權，請向他們說明如何對 [群組或應用程式審查您自己的存取權](review-your-access.md)。

如果您已將來賓指派為審核者，但未接受邀請，他們將不會收到來自存取評論的電子郵件，因為他們必須先接受邀請，才能進行審核。

## <a name="access-review-status-table"></a>存取審核狀態資料表

| 狀態 | 定義 |
|--------|------------|
|NotStarted | 已建立審核，使用者探索正在等候開始。 |
|正在初始化   | 正在進行使用者探索，以識別屬於審核部分的所有使用者。 |
|啟動中 | 正在開始評論。 如果已啟用電子郵件通知，則會將電子郵件傳送給審核者。 |
|InProgress | 已開始評論。 如果電子郵件通知已啟用，則電子郵件已傳送給審核者。 審核者可以在到期日之前提交決策。 |
|完成 | 正在完成審核，正在將電子郵件傳送給審核擁有者。 |
|自動檢查 | 審核是在系統審核階段。 系統正在記錄未依據建議或預先設定的決策進行審核的使用者的決策。 |
|自動審核 | 系統已針對所有未進行審核的使用者記錄這些決策。 如果已啟用自動 **套用，則** 審核已準備好繼續套用。 |
|應用 | 已核准的使用者將不會變更存取權。 |
|已套用 | 已拒絕的使用者（如果有的話）已從資源或目錄中移除。 |
|Failed | 無法進行評論。 此錯誤可能與刪除租使用者、授權變更或其他內部租使用者變更相關。 |

## <a name="create-reviews-via-apis"></a>透過 API 建立檢閱

您也可以使用 API 來建立存取權檢閱。 您在 Azure 入口網站中為群組和應用程式使用者的存取權檢閱所做的管理工作，也可以使用 Microsoft Graph API 來執行。 如需詳細資訊，請參閱 [Azure AD 存取審核 API 參考](/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 如需程式碼範例，請參閱透過 [Microsoft Graph 抓取 Azure AD 存取評論的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>後續步驟

- [檢閱群組或應用程式的存取權](perform-access-review.md)
- [查看群組或應用程式的存取權](review-your-access.md)
- [完成群組或應用程式的存取權檢閱](complete-access-review.md)