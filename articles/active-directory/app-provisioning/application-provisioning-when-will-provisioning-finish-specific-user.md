---
title: 瞭解特定使用者何時能夠訪問應用
description: 如何查明非常重要的使用者何時能夠存取您已設定的應用程式，以透過 Azure AD 進行使用者佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264137"
---
# <a name="check-the-status-of-user-provisioning"></a>檢查使用者預配的狀態

Azure AD 預配服務針對源系統和目標系統運行初始預配週期，然後是定期增量週期。 為應用配置預配時，可以檢查預配服務的目前狀態，並查看使用者何時能夠訪問應用。

## <a name="view-the-provisioning-progress-bar"></a>查看預配進度列

 在應用的**預配**頁上，您可以查看 Azure AD 預配服務的狀態。 頁面底部的 **"目前狀態**"部分顯示預配週期是否已開始預配使用者帳戶。 您可以查看週期的進度，查看已預配的使用者和組數，並查看創建了多少個角色。

首次配置自動預配時，頁面底部的 **"目前狀態**"部分將顯示初始預配週期的狀態。 每次運行增量迴圈時，本節都會更新。 將顯示以下詳細資訊：
- 當前運行或上次完成的預配週期類型（初始或增量）。
- 顯示已完成的預配週期的百分比的**進度條**。 百分比反映預配的頁面計數。 請注意，每個頁面可以包含多個使用者或組，因此該百分比不會與預配的使用者、組或角色的數量直接相關。
- 可用於保持視圖更新的 **"刷新**"按鈕。
- 連接器資料存儲中**的使用者**和**組**數。 每當將物件添加到預配範圍時，計數就會增加。 如果使用者是虛刪除或實刪除，則計數不會下降，因為它不會從連接器資料存儲中刪除物件。 重新計數將在 CDS 重置後第一次同步[中重新使用](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- **查看稽核記錄**連結，它打開 Azure AD 預配日誌，瞭解有關使用者預配服務運行的所有操作的詳細資訊，包括單個使用者的預配狀態（請參閱下面的["使用預配日誌"](#use-provisioning-logs-to-check-a-users-provisioning-status)部分）。

預配週期完成後，"**統計到日期"** 部分顯示迄今為止預配的使用者和組的累計數量，以及最後一個週期的完成日期和持續時間。 **活動 ID**唯一標識最新的預配週期。 **作業 ID**是預配作業的唯一識別碼，特定于租戶中的應用。

可以在 Azure 門戶 **（Azure&gt;活動目錄企業應用應用程式&gt;\[名稱\]&gt;預配）** 中查看預配進度。

![預配頁面進度列](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>使用預配日誌檢查使用者的預配狀態

要查看所選使用者的預配狀態，請參閱 Azure AD 中的[預配日誌（預覽）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 使用者預配服務運行的所有操作都記錄在 Azure AD 預配日誌中。 這包括對源和目標系統進行的所有讀寫操作，以及在每個操作期間讀取或寫入的使用者資料。

通過在 **"活動"** 部分中選擇**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽），** 可以在 Azure 門戶中訪問預配日誌。 您可以根據使用者名稱或源系統或目標系統中的識別碼搜索預配資料。 有關詳細資訊，請參閱[預配日誌（預覽）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

預配日誌記錄預配服務執行的所有操作，包括：

* 查詢位於佈建範圍內之已指派使用者的 Azure AD
* 查詢目標應用程式以確定那些使用者是否存在
* 比較系統之間的使用者物件
* 根據比較，在目標系統中新增、更新或停用使用者帳戶

有關如何讀取 Azure 門戶中的預配日誌的詳細資訊，請參閱[預配報告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>佈建使用者需要多久時間？
當對應用程式使用自動使用者預配時，Azure AD 會根據[使用者和組分配](../manage-apps/assign-user-or-group-access-portal.md)等常規時間間隔（通常每 40 分鐘）預配和更新應用中的使用者帳戶。

預配給定使用者所需的時間主要取決於預配作業是運行初始週期還是增量週期。

- 對於**初始週期**，作業時間取決於許多因素，包括預配範圍內的使用者和組數，以及源系統中的使用者和組總數。 根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，Azure AD 和應用程式之間的第一次同步處理可能會花費 20 分鐘到數小時。 本節稍後將概述影響初始週期性能的因素的完整清單。

- 對於初始**週期後的增量週期**，作業時間往往更快（例如，在 10 分鐘內），因為預配服務存儲的浮水印，表示初始週期後兩個系統的狀態，從而提高後續同步的性能。 作業時間取決於該預配週期中檢測到的更改數。 如果使用者或組成員身份更改少於 5，000 個，則作業可以在單個增量預配週期內完成。 

下表摘要說明常見佈建案例的同步處理時間。 在這些案例中，來源系統是 Azure AD，而目標系統是 SaaS 應用程式。 同步時間派生自 SaaS 應用程式 ServiceNow、工作場所、Salesforce 和 G Suite 的同步作業的統計分析。


| 範圍設定 | 範圍中的使用者、群組和成員 | 初始迴圈時間 | 增量迴圈時間 |
| -------- | -------- | -------- | -------- |
| 僅同步已指派的使用者與群組 |  < 1,000 |  < 30 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |  1,000 - 10,000 | 142 - 708 分鐘 | < 30 分鐘 |
| 僅同步已指派的使用者與群組 |   10,000 - 100,000 | 1,170 - 2,340 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  < 1,000 | < 30 分鐘  | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  1,000 - 10,000 | < 30 - 120 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者和群組 |  10,000 - 100,000  | 713 - 1,425 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者|  < 1,000  | < 30 分鐘 | < 30 分鐘 |
| 同步 Azure AD 中的所有使用者 | 1,000 - 10,000  | 43 - 86 分鐘 | < 30 分鐘 |


對於僅對**配置同步分配的使用者和組**，可以使用以下公式來確定大致最小和最大預期**初始週期**時間：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影響完成**初始週期**所需的時間的因素摘要：

- 佈建範圍中的使用者和群組總數。

- 來源系統 (Azure AD) 中存在的使用者、群組和群組成員總數。

- 預配範圍內的使用者是否與目標應用程式中的現有使用者匹配，還是需要首次創建。 首次創建所有使用者的同步作業的時間大約是所有使用者與現有使用者匹配的同步作業的*兩倍*。

- [預配日誌](check-status-user-account-provisioning.md)中的錯誤數。 如果有許多錯誤且佈建服務已進入隔離狀態，效能就會變差。 

- 目標系統實作的要求速率限制和節流設定。 某些目標系統實現請求速率限制和限制，這可能會影響大型同步操作期間的性能。 在這樣的情況下，太快收到太多要求的應用程式，可能會因此降低其回應速率或關閉連線。 若要改善效能，連接器必須進行調整，傳送應用程式要求的速度不可比應用程式處理這些要求的速度快。 由 Microsoft 所建置的佈建連接器會進行這項調整。 

- 指派群組的數目和大小。 同步指派群組所花的時間可能比同步使用者的時間長。 指派群組的數目和大小會影響效能。 如果應用程式[啟用群組物件同步處理的對應](customize-application-attributes.md#editing-group-attribute-mappings)，則除了使用者外，群組屬性 (例如群組名稱和成員資格) 也會一起同步。 比起只同步使用者物件，這些額外的同步處理將會花費更長時間。

- 如果性能成為問題，並且您正在嘗試預配租戶中的大多數使用者和組，請使用範圍篩選器。 範圍設定篩選條件可讓您根據特定的屬性值篩選出使用者，以微調佈建服務從 Azure AD 擷取的資料。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>後續步驟
[使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)
