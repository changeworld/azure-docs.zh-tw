---
title: 瞭解特定使用者何時可以存取應用程式
description: 如何查明非常重要的使用者何時能夠存取您已設定的應用程式，以透過 Azure AD 進行使用者佈建
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 307a97b71fe453c89617a86a88063e60fcf28fa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235055"
---
# <a name="check-the-status-of-user-provisioning"></a>檢查使用者布建的狀態

Azure AD 布建服務會對來源系統和目標系統執行初始布建週期，後面接著定期遞增週期。 當您設定應用程式的布建時，您可以檢查布建服務的目前狀態，並查看使用者何時將能存取應用程式。

## <a name="view-the-provisioning-progress-bar"></a>查看布建進度列

 在應用程式的 [布建 **] 頁面上** ，您可以查看 Azure AD 布建服務的狀態。 頁面底部的 [ **目前狀態** ] 區段會顯示布建週期是否已開始布建使用者帳戶。 您可以監看迴圈的進度、查看已布建的使用者和群組數目，以及查看有多少角色已建立。

當您第一次設定自動布建時，頁面底部的 [ **目前狀態** ] 區段會顯示初始布建週期的狀態。 此區段會在每次執行增量迴圈時進行更新。 以下是顯示的詳細資料：
- 布建週期的類型 (初始或增量) 目前正在執行或上次完成。
- 顯示已完成之布建週期百分比的 **進度** 列。 百分比會反映布建的頁面計數。 請注意，每個頁面可能包含多個使用者或群組，因此百分比不會直接與布建的使用者、群組或角色數目相互關聯。
- 您可以使用 [重新整理 **] 按鈕來** 保持視圖的更新。
- 連接器資料存放區中的 **使用者** 和 **群組** 數目。 當物件加入至布建的範圍時，計數就會增加。 如果使用者已虛刪除或遭到刪除，此計數將不會關閉，因為這不會將物件從連接器資料存放區中移除。 [重設](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)cd 之後，會重新計算第一次同步處理的計數 
- **View Audit Logs** link 會開啟 Azure AD 布建記錄檔，以取得使用者布建服務所執行之所有作業的詳細資料，包括個別使用者的布建狀態 (請參閱以下) 的 [使用布建[記錄](#use-provisioning-logs-to-check-a-users-provisioning-status)] 區段。

布建週期完成後，[ **至今的統計資料]** 區段會顯示已布建到日期的使用者和群組累計數目，以及最後一個週期的完成日期和持續時間。 **活動識別碼**可唯一識別最新的布建週期。 **作業**識別碼是布建作業的唯一識別碼，而且是租使用者中應用程式專用的識別碼。

布建進度可在 Azure 入口網站的 [ **Azure Active Directory &gt; 企業應用 &gt; \[ 程式名稱 \] &gt; ** 布建] 索引標籤中看到。

![布建頁面進度列](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>使用布建記錄來檢查使用者的布建狀態

若要查看所選使用者的布建狀態，請參閱 Azure AD 中的布建 [記錄 (預覽) ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。 使用者布建服務所執行的所有作業都會記錄在 Azure AD 布建記錄檔中。 這包括對來源和目標系統進行的所有讀取和寫入作業，以及在每個作業期間讀取或寫入的使用者資料。

您可以在 [活動] 區段中選取 [ **Azure Active Directory** &gt; **企業應用程式**布建 &gt; **記錄] (預覽) ** **Activity** ，以存取 Azure 入口網站中的布建記錄。 您可以根據使用者的名稱或來源系統或目標系統中的識別碼來搜尋布建資料。 如需詳細資訊，請參閱 [ (預覽版布建記錄) ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

布建記錄會記錄布建服務所執行的所有作業，包括：

* 查詢位於佈建範圍內之已指派使用者的 Azure AD
* 查詢目標應用程式以確定那些使用者是否存在
* 比較系統之間的使用者物件
* 根據比較，在目標系統中新增、更新或停用使用者帳戶

如需有關如何讀取 Azure 入口網站中布建記錄檔的詳細資訊，請參閱布建 [報告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？
搭配應用程式使用自動使用者布建時，Azure AD 會根據定期排程時間間隔的 [使用者和群組指派](../manage-apps/assign-user-or-group-access-portal.md) 等專案，在應用程式中自動布建和更新使用者帳戶，通常是每隔40分鐘。

布建指定使用者所需的時間，主要取決於您的布建作業是執行初始週期或增量週期。

- 針對 **初始週期**，作業時間取決於許多因素，包括布建範圍中的使用者和群組數目，以及來源系統中的使用者和群組總數。 根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，Azure AD 和應用程式之間的第一次同步處理可能會花費 20 分鐘到數小時。 本節稍後會摘要說明影響初始週期效能之因素的完整清單。

- 在初始迴圈之後的累加 **週期** 中，作業時間通常會更快 (例如在10分鐘內) ，因為布建服務會儲存浮水印，以代表兩個系統在初始迴圈之後的狀態，進而改善後續同步處理的效能。 作業時間取決於在該布建週期中偵測到的變更數目。 如果使用者或群組成員資格變更少於5000，則工作可以在單一增量布建週期內完成。 

下表摘要說明常見佈建案例的同步處理時間。 在這些案例中，來源系統是 Azure AD，而目標系統是 SaaS 應用程式。 同步處理時間是衍生自 SaaS 應用程式 ServiceNow、Workplace、Salesforce 和 G Suite 之同步處理作業的統計分析。


| 範圍設定 | 範圍中的使用者、群組和成員 | 初始週期時間 | 增量週期時間 |
| -------- | -------- | -------- | -------- |
| 僅同步已指派的使用者與群組 |  < 1,000 |  < 30 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |  1,000 - 10,000 | 142 - 708 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |   10,000 - 100,000 | 1,170 - 2,340 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  < 1,000 | < 30 分鐘  | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  1,000 - 10,000 | < 30 - 120 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  10,000 - 100,000  | 713 - 1,425 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者|  < 1,000  | < 30 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者 | 1,000 - 10,000  | 43 - 86 分鐘 | < 30 分鐘 |

針對 **僅限設定同步指派的使用者和群組**，您可以使用下列公式來判斷預期的 **初始週期** 時間大約最小和最大值：

- 最小分鐘數 = 0.01 x [指派的使用者、群組和群組成員數目]
- 最大分鐘數 = 0.08 x [指派的使用者、群組和群組成員數目]

影響完成 **初始週期**所需時間的因素摘要：

- 佈建範圍中的使用者和群組總數。

- 來源系統 (Azure AD) 中存在的使用者、群組和群組成員總數。

- 布建範圍中的使用者是否符合目標應用程式中的現有使用者，或需要在第一次建立。 每次建立所有使用者的同步作業大約需要 *兩次* ，只要將所有使用者都與現有使用者進行比對的同步作業即可。

- 布建 [記錄](check-status-user-account-provisioning.md)檔中的錯誤數目。 如果有許多錯誤且佈建服務已進入隔離狀態，效能就會變差。 

- 目標系統實作的要求速率限制和節流設定。 某些目標系統會執行要求速率限制和節流，這可能會影響大型同步作業期間的效能。 在這樣的情況下，太快收到太多要求的應用程式，可能會因此降低其回應速率或關閉連線。 若要改善效能，連接器必須進行調整，傳送應用程式要求的速度不可比應用程式處理這些要求的速度快。 由 Microsoft 所建置的佈建連接器會進行這項調整。 

- 指派群組的數目和大小。 同步指派群組所花的時間可能比同步使用者的時間長。 指派群組的數目和大小會影響效能。 如果應用程式[啟用群組物件同步處理的對應](customize-application-attributes.md#editing-group-attribute-mappings)，則除了使用者外，群組屬性 (例如群組名稱和成員資格) 也會一起同步。 比起只同步使用者物件，這些額外的同步處理將會花費更長時間。

- 如果效能變成問題，而您嘗試在您的租使用者中布建大部分的使用者和群組，請使用範圍篩選器。 範圍設定篩選條件可讓您根據特定的屬性值篩選出使用者，以微調佈建服務從 Azure AD 擷取的資料。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)