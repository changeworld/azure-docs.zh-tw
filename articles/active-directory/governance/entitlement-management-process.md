---
title: 要求流程 & 通知-Azure AD 權利管理
description: 瞭解存取套件的要求程式，以及在 Azure Active Directory 權利管理中傳送電子郵件通知的時間。
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
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746230"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中要求進程和電子郵件通知

當使用者提交要求至存取套件時，處理常式就會開始傳遞該存取權要求。 Azure AD 權利管理會在程式中發生重要事件時，將電子郵件通知傳送給核准者和要求者。 本文說明要求流程以及傳送的電子郵件通知。

## <a name="request-process"></a>要求進程

需要存取存取套件的使用者可以提交存取要求。 視原則的設定而定，要求可能需要核准。 核准要求時，進程會開始將存取套件中每個資源的存取權指派給使用者。 下圖顯示處理常式和不同狀態的總覽：

![核准流程圖表](./media/entitlement-management-process/request-process.png)

| 州 | 描述 |
| --- | --- |
| 已提交 | 使用者提交要求。 |
| 等待核准 | 如果存取套件的原則需要核准，則會將要求移至待核准。 |
| 已過期 | 如果沒有核准者在核准要求等待時間內核准要求，則要求會過期。 若要再試一次，使用者將必須重新提交其要求。 |
| 拒絕 | 核准者拒絕要求。 |
| 已核准 | 核准者核准要求。 |
| 傳遞 | 使用者 **未** 獲指派存取套件中所有資源的存取權。 如果這是外部使用者，則使用者可能尚未存取資原始目錄。 他們也可能未接受同意提示。 |
| 已傳遞 | 使用者已獲指派存取套件中所有資源的存取權。 |
| 存取擴充 | 如果原則中允許延伸，則使用者會擴充指派。 |
| 存取過期 | 使用者對存取套件的存取權已過期。 若要再次取得存取權，使用者將必須提交要求。 |

## <a name="email-notifications"></a>電子郵件通知

如果您是核准者，當您需要核准存取要求時，就會傳送電子郵件通知。 當存取要求已完成時，您也會收到通知。 您也會收到電子郵件通知，指出您的要求狀態（如果您是要求者）。

下圖顯示這些電子郵件通知何時傳送給核准者或要求者。 參考 [電子郵件通知表](entitlement-management-process.md#email-notifications-table) ，以找出圖表中所顯示之電子郵件通知的對應號碼。

### <a name="first-approvers-and-alternate-approvers"></a>第一位核准者和替代核准者
下圖顯示第一位核准者和替代核准者的體驗，以及在要求程式期間收到的電子郵件通知：

![第一個和替代核准者程式流程](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>要求者
下圖顯示要求者的體驗，以及在要求程式期間收到的電子郵件通知：

![要求者程式流程](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2-階段核准
下圖顯示第1階段和第2階段核准者的體驗，以及在要求程式期間收到的電子郵件通知：

![2-階段核准程式流程](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>電子郵件通知資料表
下表提供每個電子郵件通知的詳細資料。 您可以使用規則來管理這些電子郵件。 例如，您可以在 Outlook 中建立規則，將電子郵件移至資料夾，如果主旨包含此表格中的單字：

| # | 電子郵件主旨 | 傳送時 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 必要動作：依 *[日期]* 核准或拒絕轉送的要求 | 這封電子郵件將會傳送給第1階段的替代核准者 (在提出要求之後) 採取行動。 | 第1階段替代核准者 |
| 2 | 需要的動作：依 *[日期]* 核准或拒絕要求 | 這封電子郵件將會傳送給第一個核准者（如果已停用呈報），以採取動作。 | 第一位核准者 |
| 3 | 提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 如果已停用呈報，則會傳送此提醒電子郵件給第一個核准者。 電子郵件會要求他們在未執行動作時採取動作。 | 第一位核准者 |
| 4 | 依 [ *時間]* 核准或拒絕要求 *[日期]* | 這封電子郵件將會傳送給第一個核准者 (如果已啟用呈報) 可採取動作。 | 第一位核准者 |
| 5 | 動作所需的提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 此提醒電子郵件將會傳送給第一個核准者（如果已啟用呈報）。 電子郵件會要求他們在未執行動作時採取動作。 | 第一位核准者 |
| 6 | *[Access_package]* 的要求已過期 | 這封電子郵件會在要求到期後傳送給第一個核准者和第1階段的替代核准者。 | 第1個核准者，第1階段替代核准者 |
| 7 | 要求核准 [要求者 *]* 至 *[access_package]* | 這封電子郵件會在要求完成時傳送給第一個核准者和第1階段的替代核准者。 | 第1個核准者，第1階段替代核准者 |
| 8 | 要求核准 [要求者 *]* 至 *[access_package]* | 這封電子郵件會在第1階段要求獲得核准時，傳送給第一個核准者和第1階段的替代核准者。 | 第1個核准者，第1階段替代核准者 |
| 9 | 拒絕要求至 *[access_package]* | 這封電子郵件會在要求遭到拒絕時傳送給要求者 | 要求者 |
| 10 | 您的要求已于 *[access_package]* 到期 | 這封電子郵件會在單一或2階段要求結束時傳送給要求者。 電子郵件會通知要求者要求已過期。 | 要求者 |
| 11 | 需要的動作：依 *[日期]* 核准或拒絕要求 | 如果已停用呈報，則會將此電子郵件傳送給第二個核准者，以採取動作。 | 第二個核准者 |
| 12 | 動作所需的提醒：依 *[日期]* 核准或拒絕要求 | 如果已停用呈報，此提醒電子郵件將會傳送給第二個核准者。 通知會要求他們在尚未採取動作時採取動作。 | 第二個核准者 |
| 13 | 必要動作： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 這封電子郵件將會傳送給第二個核准者（如果已啟用呈報），以採取動作。 | 第二個核准者 |
| 14 | 動作所需的提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 如果已啟用呈報，則會傳送此提醒電子郵件給第二個核准者。 通知會要求他們在尚未採取動作時採取動作。 | 第二個核准者 |
| 15 | 必要動作：依 *[日期]* 核准或拒絕轉送的要求 | 這封電子郵件將會傳送給第2階段的替代核准者（如果已啟用呈報），以採取動作。 | 第2階段替代核准者 |
| 16 | 要求核准 [要求者 *]* 至 *[access_package]* | 這封電子郵件會在核准要求時傳送給第二個核准者和第2階段替代核准者。 | 第二個核准者，第2階段替代核准者 |
| 17 | *[Access_package]* 的要求已過期 | 這封電子郵件會在要求到期之後傳送給第二個核准者或替代核准者。 | 第二個核准者，第2階段替代核准者 |
| 18 | 您現在可以存取 *[access_package]* | 這封電子郵件將會傳送給終端使用者，以開始使用其存取權。 | 要求者 |
| 19 | 依 *[日期]* 延伸 *[access_package]* 的存取權 | 這封電子郵件會在使用者存取權到期前傳送給終端使用者。 | 要求者 |
| 20 | *[Access_package]* 的存取已結束 | 這封電子郵件會在使用者存取權到期之後傳送給終端使用者。 | 要求者 |

### <a name="access-request-emails"></a>存取要求電子郵件

當要求者提交設定為需要核准之存取套件的存取要求時，新增至原則的所有核准者都會收到一封電子郵件通知，其中包含要求的詳細資料。 電子郵件中的詳細資料包括：要求者的名稱組織和業務理由;和要求的存取開始日期和結束日期 (（如果有提供) ）。 詳細資料也會包含提交要求和要求到期的時間。

電子郵件包含連結核准者可以按一下以移至我的存取權核准或拒絕存取要求。 以下是傳送給第一個核准者或第二個核准者的範例電子郵件通知 (如果已啟用2階段核准) 完成存取要求：

![核准存取套件電子郵件的要求](./media/entitlement-management-shared/approver-request-email.png)

核准者也可以收到提醒電子郵件。 此電子郵件會要求核准者對要求進行決策。 以下是核准者收到的範例電子郵件通知，提醒他們採取動作：

![提醒存取要求電子郵件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>替代核准者要求電子郵件

如果 [替代核准者] 設定已啟用，但要求仍處於擱置狀態，則會將它轉寄。 替代核准者會收到核准或拒絕要求的電子郵件。 您可以啟用第1階段和第2階段中的替代核准者。 以下是替代核准者收到的通知範例電子郵件：

![替代核准者要求電子郵件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

核准者和替代核准者都可以核准或拒絕要求。

### <a name="approved-or-denied-emails"></a>核准或拒絕的電子郵件

 當核准者收到要求者提交的存取要求時，他們可以核准或拒絕存取要求。 核准者必須為他們的決策新增業務理由。 以下是在核准要求之後傳送給核准者和替代核准者的電子郵件範例：

![存取套件電子郵件的核准要求](./media/entitlement-management-process/approver-request-email-approved.png)

當存取要求已通過核准，並已布建其存取權時，就會傳送電子郵件通知給要求者，告知他們現在可以存取存取套件。 以下是當授與存取套件存取權時，傳送給要求者的電子郵件通知範例：

![核准的要求者存取要求電子郵件](./media/entitlement-management-process/requestor-email-approved.png)

當存取要求遭到拒絕時，會傳送電子郵件通知給要求者。 以下是當拒絕存取要求時傳送給要求者的電子郵件通知範例：

![要求者要求拒絕電子郵件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2-階段核准存取要求電子郵件

如果已啟用2階段核准，則至少有兩個核准者必須核准要求（每個階段一個），要求者才能接收存取權。

在階段1中，第一位核准者會收到存取要求電子郵件並做出決定。 如果他們核准要求，則第1階段中的所有第一位核准者和替代核准者 (是否啟用呈報) 將會收到第1階段完成的通知。 以下是第1階段完成時所傳送通知的範例電子郵件：

![2-階段存取要求電子郵件](./media/entitlement-management-process/approver-request-email-2stage.png)

第一個或其他核准者在第1階段核准要求之後，第2階段開始。 在階段2中，第二個核准者會收到存取要求通知電子郵件。 在第二個核准者或第2階段的替代核准者之後 (如果啟用呈報) 決定要核准或拒絕要求，則會將通知電子郵件傳送給第一個和第二個核准者，以及第1階段和第2階段中的所有替代核准者，以及要求者。

### <a name="expired-access-request-emails"></a>過期的存取要求電子郵件

如果沒有核准者核准或拒絕要求，存取要求可能會過期。 

當要求到達其設定的到期日並到期時，核准者將無法再核准或拒絕該要求。 以下是已啟用2階段核准) 和替代核准者時，傳送給所有第一個第二個 (的通知的電子郵件範例：

![核准者過期的存取要求電子郵件](./media/entitlement-management-process/approver-request-email-expired.png)

系統也會傳送電子郵件通知給要求者，通知他們其存取要求已過期，且需要重新提交存取要求。 下圖顯示要求者的體驗，以及他們要求擴充存取權時所收到的電子郵件通知：

![要求者延伸存取程式流程](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

以下是當使用者的存取要求過期時，傳送給要求者的電子郵件通知範例：

![要求者過期的存取要求電子郵件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>後續步驟

- [要求存取存取套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)
