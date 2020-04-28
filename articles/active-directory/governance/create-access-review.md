---
title: 建立群組 & 應用程式的存取權審查-Azure AD
description: 瞭解如何在 Azure Active Directory 存取評論中建立群組成員或應用程式存取的存取權審查。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e01951b6147cfc39fe6c46035db822071bda3aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984060"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 存取評論中建立群組和應用程式的存取權審查

員工和來賓對於群組和應用程式的存取權會隨著時間而變更。 為了降低過時存取權指派的相關風險，系統管理員可以使用 Azure Active Directory (Azure AD)，建立群組成員或應用程式存取的存取權檢閱。 如果您需要定期檢閱存取權，您也可以建立週期性存取權檢閱。 如需關於這些案例的詳細資訊，請參閱[管理使用者存取權](manage-user-access-with-access-reviews.md)和[管理來賓存取權](manage-guest-access-with-access-reviews.md)。

本文說明如何建立群組成員或應用程式存取的一或多個存取權審查。

## <a name="prerequisites"></a>Prerequisites

- Azure AD Premium P2
- 全域管理員或使用者管理員

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>建立一或多個存取評論

1. 登入 Azure 入口網站並開啟 [身分[識別管理] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下 [**存取評論**]。

1. 按一下 [新增存取權檢閱]****，以建立新的存取權檢閱。

    ![存取身分識別治理中的審查窗格](./media/create-access-review/access-reviews.png)

1. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/create-access-review/name-description.png)

1. 設定 [開始日期]****。 根據預設，存取權檢閱會在其建立的相同時間開始，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

    ![建立存取權檢閱 - 開始和結束日期](./media/create-access-review/start-end-dates.png)

1. 若要定期進行存取權檢查，請**將頻率**設定**從一次**變更為**每週**、**每月**、每**季**、每**半年**或**每年**。 使用 [**持續時間**] 滑杆或文字方塊，定義每次審核重複數列的天數，以供審核者輸入。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。

1. 使用 [結束]**** 設定來指定如何結束週期性存取權檢閱系列。 此系列的結束方式有三種：它會持續執行而無限期地啟動檢閱、直到特定日期為止，或是在完成所定義的發生次數之後。 您、其他使用者的系統管理員或其他全域管理員可以藉由變更 [**設定**] 中的日期，讓該數列在建立後停止，使其在該日期結束。

1. 在 [**使用者**] 區段中，指定要套用存取審核的使用者。 存取權檢閱可針對群組成員或指派給應用程式的使用者執行。 您可以進一步將存取權檢閱限縮成僅檢閱身為成員 (或指派給應用程式) 的來賓使用者，而非檢閱身為成員或可存取應用程式的所有使用者。

    ![建立存取權檢閱 - 使用者](./media/create-access-review/users.png)

1. 在 [**群組**] 區段中，選取您想要查看其成員資格的一或多個群組。

    > [!NOTE]
    > 選取一個以上的群組將會建立多個存取審查。 例如，選取五個群組會建立五個不同的存取審查。
    
    ![建立存取權審查-選取群組](./media/create-access-review/select-group.png)

1. 在 [**應用程式**] 區段中（如果您已在步驟8中選取 [**指派給應用程式**]），請選取您要檢查其存取權的應用程式。

    > [!NOTE]
    > 選取一個以上的應用程式將會建立多個存取審查。 例如，選取五個應用程式會建立五個不同的存取審查。
    
    ![建立存取權審查-選取應用程式](./media/create-access-review/select-application.png)

1. 在 [檢閱者]**** 區段中，選取一或多個人員來檢閱範圍內的所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。 如果資源是群組，您可以要求群組擁有者檢閱。 您也可以要求檢閱者在核准存取權時提供原因。

    ![建立存取權檢閱 - 檢閱者](./media/create-access-review/reviewers.png)

1. 在 [程式]**** 區段中，選取您要使用的程式。 一律會出現一個「預設程式」****。

    ![建立存取權檢閱 - 程式](./media/create-access-review/programs.png)

    您可以將追蹤和收集不同用途的存取權檢閱簡化，方法是將它們組織到程式中。 每個存取權檢閱可連結至一個程式。 然後當您為稽核員準備報告時，您可以著重於特定方案範圍內的存取權檢閱。 全域管理員、使用者系統管理員、安全性系統管理員或安全性讀取者角色中的使用者可以看見程式和存取權審查結果。

    若要查看程式清單，請移至存取權檢閱頁面，並選取 [程式集]****。 如果您是全域管理員或使用者系統管理員角色，則可以建立其他程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。 如果您不再需要某個程式，且該程式未連結任何控制項，就可以將程式刪除。

### <a name="upon-completion-settings"></a>完成時的設定

1. 若要指定檢閱完成後所應執行的動作，請展開 [完成時的設定]**** 區段。

    ![建立存取權審查-完成後的設定](./media/create-access-review/upon-completion-settings.png)

1. 如果您想要自動移除，存取被拒絕的使用者，請將 [**自動將結果套用至資源**] 設定為 [**啟用**]。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]****。

1. 使用 [若檢閱者未回應]**** 清單，指定檢閱者在檢閱期間內未檢閱的使用者將受到何種處置。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

    - **無變更** - 使用者的存取權保持不變
    - **移除存取權** - 移除使用者的存取權
    - **核准存取權** - 核准使用者的存取權
    - **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

### <a name="advanced-settings"></a>進階設定

1. 若要指定其他設定，請展開 [進階設定]**** 區段。

    ![建立存取權審查-Advanced 設定](./media/create-access-review/advanced-settings.png)

1. 將 [顯示建議]**** 設為 [啟用]****，會向檢閱者顯示系統根據使用者的存取資訊所做的建議。

1. 將 [需要核准的原因]**** 設為 [啟用]****，會要求檢閱者提供核准原因。

1. 將 [郵件通知]**** 設為 [啟用]****，會讓 Azure AD 在存取權檢閱開始時傳送電子郵件通知給檢閱者，並在檢閱完成時傳送電子郵件通知給管理員。

1. 將 [提醒]**** 設為 [啟用]****，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。

    依預設，Azure AD 會在結束日期過半時自動將提醒傳送給尚未回應的檢閱者。

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]****。 存取權審查會出現在您的清單中，並顯示其狀態的指標。

![存取評論及其狀態的清單](./media/create-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們顯示如何[審查群組或應用程式存取權](perform-access-review.md)的指示。 如果您的評論是讓來賓審查自己的存取權，請向他們說明如何對[群組或應用程式檢查自己的存取權](review-your-access.md)。

如果您已將來賓指派為審核者，而他們尚未接受邀請，他們將不會收到來自存取審查的電子郵件，因為他們必須先接受邀請，然後再進行審查。

## <a name="access-review-status-table"></a>存取審查狀態資料表

| 狀態 | 定義 |
|--------|------------|
|NotStarted | 已建立審查，使用者探索正在等候啟動。 |
|正在初始化   | 使用者探索正在進行中，以識別屬於評論的所有使用者。 |
|啟動中 | 正在開始審查。 如果已啟用電子郵件通知，則會將電子郵件傳送給審核者。 |
|InProgress | 已開始審查。 如果已啟用電子郵件通知，則會將電子郵件傳送給審核者。 審核者可以在到期日之前提交決策。 |
|' | 正在完成審查，並將電子郵件傳送給審核擁有者。 |
|AutoReviewing | 審查是在「系統檢查」階段。 系統會針對未根據建議或預先設定的決策進行審核的使用者記錄決策。 |
|AutoReviewed | 系統會針對所有未審核的使用者，記錄其決策。 如果已啟用自動套用，則審查已準備就緒，可繼續**申請**。 |
|于 | 對於已核准的使用者，將不會變更存取權。 |
|已套用 | 已拒絕的使用者（如果有的話）已從資源或目錄中移除。 |

## <a name="create-reviews-via-apis"></a>透過 API 建立檢閱

您也可以使用 API 來建立存取權檢閱。 您在 Azure 入口網站中為群組和應用程式使用者的存取權檢閱所做的管理工作，也可以使用 Microsoft Graph API 來執行。 如需詳細資訊，請參閱[Azure AD 存取評論 API 參考](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 如需程式碼範例，請參閱透過[Microsoft Graph 抓取 Azure AD 存取評論的範例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>後續步驟

- [審查群組或應用程式的存取權](perform-access-review.md)
- [對群組或應用程式檢查自己的存取權](review-your-access.md)
- [完成群組或應用程式的存取權審查](complete-access-review.md)
