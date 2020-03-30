---
title: 請求流程&通知 - Azure AD 授權管理
description: 瞭解訪問包的請求過程以及 Azure 活動目錄授權管理中發送電子郵件通知的時間。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128529"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中請求進程和電子郵件通知

當使用者向訪問包提交請求時，進程將開始傳遞該訪問請求。 Azure AD 授權管理在過程中發生關鍵事件時向核准者和要求者發送電子郵件通知。 本文介紹了請求過程和發送的電子郵件通知。

## <a name="request-process"></a>請求流程

需要訪問包的使用者可以提交訪問請求。 根據策略的配置，請求可能需要批准。 批准請求後，進程將開始將使用者存取權限分配給訪問包中的每個資源。 下圖顯示了流程和不同狀態的概述：

![審批流程圖](./media/entitlement-management-process/request-process.png)

| State | 描述 |
| --- | --- |
| 已提交 | 使用者提交請求。 |
| 等待核准 | 如果訪問包的策略需要審批，則請求將移動到待審批。 |
| 已過期 | 如果沒有核准者在核准請求超時內批准請求，則請求將過期。 要重試，使用者必須重新提交其請求。 |
| 拒絕 | 核准者拒絕請求。 |
| 已核准 | 審批人批准請求。 |
| 傳遞 | **尚未**為使用者分配對訪問包中所有資源的存取權限。 如果這是外部使用者，則使用者可能尚未訪問資原始目錄。 他們也可能不接受同意提示。 |
| 已傳遞 | 已為使用者分配對訪問包中所有資源的存取權限。 |
| 訪問擴展 | 如果策略中允許擴展，則使用者擴展分配。 |
| 訪問已過期 | 使用者對訪問包的訪問已過期。 要再次訪問，使用者必須提交請求。 |

## <a name="email-notifications"></a>電子郵件通知

如果您是審批人，則需要批准訪問請求時向您發送電子郵件通知。 訪問請求完成後，您還會收到通知。 如果您是要求者，還會向您發送電子郵件通知，指示請求的狀態。

下圖顯示了何時將這些電子郵件通知發送給審批人或要求者。 引用[電子郵件通知表](entitlement-management-process.md#email-notifications-table)，查找圖表中顯示的電子郵件通知的相應編號。

### <a name="first-approvers-and-alternate-approvers"></a>第一批人和候補審批人
下圖顯示了第一批審批人和候補審批人的經驗，以及他們在請求過程中收到的電子郵件通知：

![第一個和備用審批人流程流程](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>要求者
下圖顯示了要求者的經驗以及他們在請求過程中收到的電子郵件通知：

![要求者流程](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2 階段批准
下圖顯示了階段 1 和階段 2 核准者的經驗以及他們在請求過程中收到的電子郵件通知：

![2 階段審批流程流程](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>電子郵件通知表
下表提供了有關每個電子郵件通知的更多詳細資訊。 要管理這些電子郵件，可以使用規則。 例如，在 Outlook 中，如果主題包含此表中的單詞，則可以創建規則將電子郵件移動到資料夾：

| # | 電子郵件主旨 | 發送時 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 所需操作：在 *[日期]* 前批准或拒絕轉發的請求 | 此電子郵件將發送給階段 1 備用審批人（在請求升級後）以執行操作。 | 階段 1 備用審批人 |
| 2 | 所需操作：在 *[日期]* 前批准或拒絕請求 | 如果禁用上報，此電子郵件將發送給第一個審批人，以執行操作。 | 第一個核准者 |
| 3 | 提醒：在 *[日期]* 前批准或拒絕 *[要求者]* 請求 | 如果禁用上報，此提醒電子郵件將發送給第一個審批人。 電子郵件要求他們採取行動，如果他們沒有。 | 第一個核准者 |
| 4 | 在 *[日期*] 上[*時間]* 批准或拒絕請求 | 此電子郵件將發送給第一個審批人（如果啟用了上報）以執行操作。 | 第一個核准者 |
| 5 | 需要操作的提醒：在 *[日期]* 前批准或拒絕 *[要求者]* 請求 | 如果啟用了上報，此提醒電子郵件將發送給第一個審批人。 電子郵件要求他們採取行動，如果他們沒有。 | 第一個核准者 |
| 6 | *[access_package]* 的請求已過期 | 此電子郵件將在請求過期後發送給第一個審批人和階段 1 備用審批人。 | 第一個審批人，第 1 階段備用審批人 |
| 7 | [*要求者]* 請求 *[access_package]* | 此電子郵件將在請求完成時發送給第一個審批人和第 1 階段備用審批人。 | 第一個審批人，第 1 階段備用審批人 |
| 8 | [*要求者]* 請求 *[access_package]* | 當階段-1 請求獲得批准時，此電子郵件將發送給第一個審批人和階段 1 備用審批人。 | 第一個審批人，第 1 階段備用審批人 |
| 9 | 拒絕 *[access_package]* 的請求 | 此電子郵件將在請求被拒絕時發送給要求者 | 要求者 |
| 10 | 您的請求已過期 *[access_package]* | 此電子郵件將在單個或 2 階段請求結束時發送給要求者。 電子郵件通知要求者請求已過期。 | 要求者 |
| 11 | 所需操作：在 *[日期]* 前批准或拒絕請求 | 如果禁用上報，此電子郵件將發送給第二個審批人，以執行操作。 | 第二個審批人 |
| 12 | 需要操作的提醒：在 *[日期]* 前批准或拒絕請求 | 如果禁用上報，此提醒電子郵件將發送給第二個審批人。 通知要求他們採取行動，如果他們還沒有。 | 第二個審批人 |
| 13 | 所需操作：在 *[日期]* 前批准或拒絕 *[要求者]* 請求 | 如果啟用上報，此電子郵件將發送給第二個審批人，以便執行操作。 | 第二個審批人 |
| 14 | 需要操作的提醒：在 *[日期]* 前批准或拒絕 *[要求者]* 請求 | 如果啟用了上報，此提醒電子郵件將發送給第二個審批人。 通知要求他們採取行動，如果他們還沒有。 | 第二個審批人 |
| 15 | 所需操作：在 *[日期]* 前批准或拒絕轉發的請求 | 如果啟用上報，此電子郵件將發送給階段 2 備用審批人，以便執行操作。 | 階段 2 備用審批人 |
| 16 | [*要求者]* 請求 *[access_package]* | 此電子郵件將在批准請求時發送給第二個審批人和第 2 階段備用審批人。 | 第二個核准者，第 2 階段備用審批人 |
| 17 | 請求已過期 *[access_package]* | 此電子郵件將在請求過期後發送給第二個審批人或候補審批人。 | 第二個核准者，第 2 階段候補審批人 |
| 18 | 您現在可以訪問 *[access_package]* | 此電子郵件將發送給最終使用者，以開始使用他們的存取權限。 | 要求者 |
| 19 | 按 *[日期]* 擴展 *[access_package]* 的訪問 | 此電子郵件將在最終使用者訪問到期之前發送給其。 | 要求者 |
| 20 | *[access_package]* 的訪問已結束 | 此電子郵件將在最終使用者的訪問過期後發送給其。 | 要求者 |

### <a name="access-request-emails"></a>訪問請求電子郵件

當要求者提交配置為需要審批的訪問包的訪問請求時，添加到策略中的所有核准者都將收到包含請求詳細資訊的電子郵件通知。 電子郵件中的詳細資訊包括：申請人的名稱組織，以及業務理由;以及請求的訪問開始日期和結束日期（如果提供）。 詳細資訊還包括提交請求的時間和請求何時過期。

電子郵件包含一個連結，核准者可以按一下以轉到"我的存取權限"以批准或拒絕訪問請求。 下面是發送給第一個審批人或第二個審批人（如果啟用了 2 階段批准）以完成訪問請求的電子郵件通知示例：

![批准訪問包電子郵件的請求](./media/entitlement-management-shared/approver-request-email.png)

核准者也可以收到提醒電子郵件。 電子郵件要求審批人就請求作出決定。 下面是核准者收到的電子郵件通知示例，以提醒他們採取行動：

![提醒訪問請求電子郵件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>備用審批人請求電子郵件

如果啟用了備用審批人設置，並且請求仍然掛起，則將轉發該請求。 候補審批人將收到一封電子郵件以批准或拒絕請求。 您可以在階段 1 和階段 2 中啟用備用審批人。 下面是備用審批人收到的通知示例電子郵件：

![候補審批人請求電子郵件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

審批人和候補審批人都可以批准或拒絕請求。

### <a name="approved-or-denied-emails"></a>已批准或拒絕的電子郵件

 當核准者收到要求者提交的訪問請求時，他們可以批准或拒絕訪問請求。 審批人需要為其決策添加業務理由。 以下是在批准請求後發送給審批人和候補審批人的示例電子郵件：

![訪問包電子郵件的已批准請求](./media/entitlement-management-process/approver-request-email-approved.png)

當訪問請求獲得批准並預配其存取權限時，將通過電子郵件通知要求者，通知他們現在有權訪問訪問包。 下面是一個示例電子郵件通知，該通知在要求者被授予訪問包存取權限時發送給他們：

![已批准的請求人訪問請求電子郵件](./media/entitlement-management-process/requestor-email-approved.png)

當訪問請求被拒絕時，將通過電子郵件發送通知給要求者。 下面是在要求者訪問請求被拒絕時發送給其電子郵件通知的示例通知：

![要求者請求被拒絕的電子郵件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2 階段審批訪問請求電子郵件

如果啟用了 2 階段批准，則至少兩個核准者必須批准請求，一個來自每個階段，然後要求者才能接收存取權限。

在第 1 階段期間，第一個核准者將收到訪問請求電子郵件並做出決定。 如果他們批准請求，則階段 1 中的所有先批准人和候補審批人（如果啟用了上報）將收到階段 1 已完成的通知。 下面是階段 1 完成後發送的通知示例電子郵件：

![2 階段訪問請求電子郵件](./media/entitlement-management-process/approver-request-email-2stage.png)

在第一個或備用審批人在第 1 階段批准請求後，階段-2 開始。 在第 2 階段期間，第二個審批人將收到訪問請求通知電子郵件。 在第 2 階段的第二個審批人或候補審批人（如果啟用了上報）決定批准或拒絕請求後，通知電子郵件將發送給第一和第二批審批人，以及階段 1 和階段 2 中的所有備用審批人以及要求者。

### <a name="expired-access-request-emails"></a>過期的訪問請求電子郵件

如果沒有審批人批准或拒絕該請求，訪問請求可能會過期。 

當請求達到其配置的到期日期並過期時，核准者無法再批准或拒絕該請求。 下面是發送給所有第一、第二（如果啟用了 2 階段批准）和備用審批人的通知的示例電子郵件：

![核准者過期訪問請求電子郵件](./media/entitlement-management-process/approver-request-email-expired.png)

還會向要求者發送電子郵件通知，通知他們其訪問請求已過期，並且他們需要重新提交訪問請求。 下圖顯示了要求者的經驗以及他們請求擴展存取權限時收到的電子郵件通知：

![要求者擴展訪問流程流](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

下面是在訪問請求過期時發送給要求者的電子郵件通知示例：

![要求者過期訪問請求電子郵件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>後續步驟

- [請求訪問包](entitlement-management-request-access.md)
- [批准或拒絕訪問請求](entitlement-management-request-approve.md)
