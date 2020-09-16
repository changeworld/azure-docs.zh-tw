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
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d1c40c26dd6b6992d8df85a986b4157a22226a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602926"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取權評論中建立群組和應用程式的存取權審核

員工和來賓對於群組和應用程式的存取權會隨著時間而變更。 為了降低過時存取權指派的相關風險，系統管理員可以使用 Azure Active Directory (Azure AD)，建立群組成員或應用程式存取的存取權檢閱。 如果您需要定期檢閱存取權，您也可以建立週期性存取權檢閱。 如需關於這些案例的詳細資訊，請參閱[管理使用者存取權](manage-user-access-with-access-reviews.md)和[管理來賓存取權](manage-guest-access-with-access-reviews.md)。

您可以觀看影片，瞭解如何啟用存取權審核：

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

本文說明如何針對群組成員或應用程式存取建立一或多個存取權審核。

## <a name="prerequisites"></a>Prerequisites

- Azure AD Premium P2
- 全域管理員或使用者管理員

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>建立一或多個存取權檢閱

1. 登入 Azure 入口網站，然後開啟 [身分 [識別管理] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下 [ **存取審核**]。

1. 按一下 [新增存取權檢閱]****，以建立新的存取權檢閱。

    ![身分識別管理中的 [存取審核] 窗格](./media/create-access-review/access-reviews.png)

1. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/create-access-review/name-description.png)

1. 設定 [開始日期]。 根據預設，存取權檢閱會在其建立的相同時間開始，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

    ![建立存取權檢閱 - 開始和結束日期](./media/create-access-review/start-end-dates.png)

1. 若要定期進行存取檢查，請將 **頻率** 設定從 **一次** 變更為 **每週**、 **每月**、每 **季**、每 **半年**或 **每年**。 使用 [持續時間] 滑桿或文字方塊，定義週期性系列的每次檢閱將開放檢閱者輸入的天數。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。

1. 使用 [結束] 設定來指定如何結束週期性存取權檢閱系列。 此系列可透過三種方式結束： 
    1. 它會持續執行以無限期地開始評論
    1. 直到特定日期為止，
    1. 直到完成定義的發生次數為止。 
  
    您、其他使用者管理員或其他全域管理員皆可以變更 [設定] 中的日期，以在系列建立之後予以停止，使其於該日期結束。

1. 在 [ **使用者** ] 區段中，指定存取權審核適用的使用者。 存取權檢閱可針對群組成員或指派給應用程式的使用者執行。 您可以進一步將存取權檢閱限縮成僅檢閱身為成員 (或指派給應用程式) 的來賓使用者，而非檢閱身為成員或可存取應用程式的所有使用者。

    ![建立存取權檢閱 - 使用者](./media/create-access-review/users.png)

1. 在 [ **群組** ] 區段中，選取一或多個您想要檢查其成員資格的群組。

    > [!NOTE]
    > 選取一個以上的群組將會建立多個存取權評論。 例如，選取五個群組將會建立五個不同的存取權評論。
    
    ![建立存取權審核-選取群組](./media/create-access-review/select-group.png)

1. 在 [ **應用程式** ] 區段中 (如果您選取 [在步驟8中 **指派給應用程式** ]) ，請選取您要檢查其存取權的應用程式。

    > [!NOTE]
    > 選取一個以上的應用程式將會建立多個存取權評論。 例如，選取五個應用程式將會建立五個不同的存取權評論。
    
    ![建立存取權審核-選取應用程式](./media/create-access-review/select-application.png)

1. 在 [檢閱者]**** 區段中，選取一或多個人員來檢閱範圍內的所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。 如果資源是群組，您可以要求群組擁有者檢閱。 您也可以要求檢閱者在核准存取權時提供原因。

    ![建立存取權檢閱 - 檢閱者](./media/create-access-review/reviewers.png)

1. 在 [程式]**** 區段中，選取您要使用的程式。 一律會出現一個「預設程式」****。

    ![建立存取權檢閱 - 程式](./media/create-access-review/programs.png)

    您可以藉由將存取評論組織成程式來簡化其收集和追蹤。 每個存取權檢閱可連結至一個程式。 然後當您為稽核員準備報告時，您可以著重於特定方案範圍內的存取權檢閱。 全域管理員、使用者系統管理員、安全性系統管理員或安全性讀取者角色中的使用者可以看見程式和存取權審核結果。

    若要查看程式清單，請移至存取權檢閱頁面，並選取 [程式集]****。 如果您是全域系統管理員或使用者系統管理員角色，您可以建立其他程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。 當您不再需要某個程式，且它沒有任何連結的控制項時，您可以將它刪除。

### <a name="upon-completion-settings"></a>完成時的設定

1. 若要指定檢閱完成後所應執行的動作，請展開 [完成時的設定] 區段。

    ![建立存取權檢查-完成設定時](./media/create-access-review/upon-completion-settings-new.png)

2. 如果您想要自動移除拒絕的使用者存取權，請將 [ **自動將結果套用至資源** ] 以 **啟用**。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]。

3. 使用 [ **如果審核者未回應** ] 清單，以指定審核期間內的審核者未審核的使用者會發生什麼事。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

    - **無變更** - 使用者的存取權保持不變
    - **移除存取權** - 移除使用者的存取權
    - **核准存取權** - 核准使用者的存取權
    - **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

    ![建立存取權審核-Advanced settings](./media/create-access-review/advanced-settings-preview-new.png)

4.  (Preview) 使用在拒絕的使用者上套用的動作，以指定當來賓使用者遭到拒絕時，會發生什麼事。
    - **選項 1** 會移除被拒絕的使用者對要審核的群組或應用程式的存取權，他們仍然可以登入租使用者。 
    - **選項 2** 會封鎖拒絕的使用者登入租使用者，而不論他們是否有其他資源的存取權。 如果發生錯誤，或系統管理員決定重新啟用一個存取權，他們就可以在使用者停用之後的30天內完成這項作業。 如果停用的使用者沒有採取任何動作，則會從租使用者中刪除。

若要深入瞭解移除不再具有您組織中資源存取權之來賓使用者的最佳作法，請閱讀標題為 [使用 Azure AD Identity Governance 來檢查和移除不再具有資源存取權的外部使用者](access-reviews-external-users.md)的相關文章。

>[!NOTE]
> 只有在您先前已將審核範圍設定為僅限來賓使用者 (請參閱 **建立一或多個存取權評論** 的步驟 8) ，才能在拒絕的使用者上套用的動作

### <a name="advanced-settings"></a>進階設定

1. 若要指定其他設定，請展開 [進階設定] 區段。

1. 將 [顯示建議] 設為 [啟用]，會向檢閱者顯示系統根據使用者的存取資訊所做的建議。

1. 將 [需要核准的原因] 設為 [啟用]，會要求檢閱者提供核准原因。

1. 將 [郵件通知] 設為 [啟用]，會讓 Azure AD 在存取權檢閱開始時傳送電子郵件通知給檢閱者，並在檢閱完成時傳送電子郵件通知給管理員。

1. 將 [提醒] 設為 [啟用]，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。 

    >[!NOTE]
    > 依預設，Azure AD 會自動將提醒傳送給尚未回應的審核者的結束日期

1.  (Preview) 傳送給審核者之電子郵件的內容會根據審核詳細資料（例如審核名稱、資源名稱、到期日等）自動產生。如果您需要一種方式來傳達額外的資訊，例如其他指示或連絡人資訊，您可以在 **評論者電子郵件的其他內容** 中指定這些詳細資料，該電子郵件會包含在傳送給指派審核者的邀請和提醒電子郵件中。 以下反白顯示的區段是將顯示這項資訊的位置。

    ![檢查使用者對群組的存取權](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]****。 存取權審核將會出現在清單中，並顯示其狀態的指標。

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

## <a name="create-reviews-via-apis"></a>透過 API 建立檢閱

您也可以使用 API 來建立存取權檢閱。 您在 Azure 入口網站中為群組和應用程式使用者的存取權檢閱所做的管理工作，也可以使用 Microsoft Graph API 來執行。 如需詳細資訊，請參閱 [Azure AD 存取審核 API 參考](/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 如需程式碼範例，請參閱透過 [Microsoft Graph 抓取 Azure AD 存取評論的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>後續步驟

- [檢閱群組或應用程式的存取權](perform-access-review.md)
- [查看群組或應用程式的存取權](review-your-access.md)
- [完成群組或應用程式的存取權檢閱](complete-access-review.md)