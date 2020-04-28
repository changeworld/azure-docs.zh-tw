---
title: 要求處理 & 通知-Azure AD 權利管理
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
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128529"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的要求處理和電子郵件通知

當使用者將要求提交至存取套件時，處理常式就會開始傳遞該存取要求。 Azure AD 權利管理會在過程中發生重要事件時，將電子郵件通知傳送給核准者和要求者。 本文說明要求程式和傳送的電子郵件通知。

## <a name="request-process"></a>要求處理

必須存取存取套件的使用者可以提交存取要求。 視原則的設定而定，要求可能需要核准。 當要求獲得核准時，處理常式會開始將存取封裝中每個資源的存取權指派給該使用者。 下圖顯示進程和不同狀態的總覽：

![核准流程圖表](./media/entitlement-management-process/request-process.png)

| State | 描述 |
| --- | --- |
| 已提交 | 使用者提交要求。 |
| 等待核准 | 如果存取套件的原則需要核准，則要求會移至 [待核准]。 |
| 已過期 | 如果沒有核准者核准要求超時內的要求，要求就會過期。 若要再試一次，使用者將必須重新提交其要求。 |
| 拒絕 | 核准者拒絕要求。 |
| 已核准 | 核准者核准要求。 |
| 傳遞 | 使用者尚未**獲**指派存取封裝中所有資源的存取權。 如果這是外部使用者，則使用者可能尚未存取資原始目錄。 他們可能也不接受同意提示。 |
| 已傳遞 | 使用者已獲指派存取封裝中所有資源的存取權。 |
| 存取擴充 | 如果原則中允許延伸模組，則使用者會擴充指派。 |
| 存取已過期 | 使用者對存取套件的存取權已過期。 若要再次取得存取權，使用者必須提交要求。 |

## <a name="email-notifications"></a>電子郵件通知

如果您是核准者，當您需要核准存取要求時，就會傳送電子郵件通知給您。 當存取要求完成時，您也會收到通知。 如果您是要求者，也會傳送電子郵件通知，指出您的要求狀態。

下圖顯示這些電子郵件通知何時傳送給核准者或要求者。 參考 [[電子郵件通知] 資料表](entitlement-management-process.md#email-notifications-table)，尋找圖表中顯示之電子郵件通知的對應號碼。

### <a name="first-approvers-and-alternate-approvers"></a>第一位核准者和替代核准者
下圖顯示第一個核准者和替代核准者的體驗，以及在要求程式期間收到的電子郵件通知：

![第一個和替代的核准者處理流程](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>要求者
下圖顯示要求者的體驗，以及在要求過程中所收到的電子郵件通知：

![要求者程式流程](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2階段核准
下圖顯示第1階段和第2階段核准者的體驗，以及在要求過程中收到的電子郵件通知：

![2階段核准程式流程](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>電子郵件通知資料表
下表提供每個電子郵件通知的詳細資訊。 若要管理這些電子郵件，您可以使用規則。 例如，您可以在 Outlook 中建立規則，將電子郵件移至資料夾（如果主旨包含此表格中的單字）：

| # | 電子郵件主旨 | 傳送時 | 傳送至 |
| --- | --- | --- | --- |
| 1 | 必要動作：依 *[日期]* 核准或拒絕轉送的要求 | 此電子郵件將會傳送給第1階段的替代核准者（在要求呈報之後）以採取動作。 | 第1階段的替代核准者 |
| 2 | 必要動作：依 *[日期]* 核准或拒絕要求 | 此電子郵件將會傳送給第一位核准者，如果已停用擴大，則採取動作。 | 第一位核准者 |
| 3 | 提醒：為 [要求者 *]* 核准或拒絕 *[date]* 要求 | 如果已停用呈報，此提醒電子郵件將會傳送給第一位核准者。 此電子郵件會要求他們採取動作（如果尚未這麼做）。 | 第一位核准者 |
| 4 | 以 [ *time]* 于 *[date]* 核准或拒絕要求 | 這封電子郵件會傳送給第一個核准者（如果已啟用擴大）以採取行動。 | 第一位核准者 |
| 5 | 必要動作提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 如果已啟用擴大，則會將此提醒電子郵件傳送給第一位核准者。 此電子郵件會要求他們採取動作（如果尚未這麼做）。 | 第一位核准者 |
| 6 | *[Access_package]* 的要求已過期 | 此電子郵件將會在要求過期後，傳送給第一個核准者和第1階段的替代核准者。 | 第一次核准者，第1階段替代核准者 |
| 7 | [要求者 *]* 已核准至 *[access_package]* | 這封電子郵件將會在要求完成時，傳送給第一個核准者和第1階段的替代核准者。 | 第一次核准者，第1階段替代核准者 |
| 8 | [要求者 *]* 已核准至 *[access_package]* | 這封電子郵件將會傳送給第一次核准者，而當階段-1 要求獲得核准時，則是2階段要求的第1階段替代核准者。 | 第一次核准者，第1階段替代核准者 |
| 9 | 拒絕要求至 *[access_package]* | 當要求遭到拒絕時，會將此電子郵件傳送給要求者 | 要求者 |
| 10 | 您的要求已過期 *[access_package]* | 這封電子郵件將會在單一或2階段要求結束時傳送給要求者。 此電子郵件會通知要求者要求已過期。 | 要求者 |
| 11 | 必要動作：依 *[日期]* 核准或拒絕要求 | 此電子郵件將會傳送給第二個核准者，如果已停用呈報，則採取動作。 | 第二核准者 |
| 12 | 必要動作提醒：依 *[日期]* 核准或拒絕要求 | 如果已停用呈報，此提醒電子郵件將會傳送給第二個核准者。 通知會要求他們採取動作（如果尚未這麼做）。 | 第二核准者 |
| 13 | 需要的動作： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 此電子郵件將會傳送給第二個核准者（如果已啟用擴大），以採取行動。 | 第二核准者 |
| 14 | 必要動作提醒： [要求者 *]* 的 *[日期]* 核准或拒絕要求 | 如果已啟用擴大，此提醒電子郵件將會傳送給第二個核准者。 通知會要求他們採取動作（如果尚未這麼做）。 | 第二核准者 |
| 15 | 必要動作：依 *[日期]* 核准或拒絕轉送的要求 | 這封電子郵件會傳送給第2階段的替代核准者（如果已啟用擴大），以採取行動。 | 第2階段替代核准者 |
| 16 | [要求者 *]* 已核准至 *[access_package]* | 這封電子郵件將會傳送給第二個核准者，並在核准要求時，第2階段替代核准者。 | 第二個核准者，第2階段替代核准者 |
| 17 | *[Access_package]* 的要求已過期 | 此電子郵件將會在要求到期後傳送給第二位核准者或替代核准者。 | 第二個核准者，第2階段替代核准者 |
| 18 | 您現在可以存取 *[access_package]* | 這封電子郵件將會傳送給使用者，以開始使用他們的存取權。 | 要求者 |
| 19 | 依 *[date]* 擴充 *[access_package]* 的存取權 | 這封電子郵件將會傳送給使用者，讓他們的存取權過期。 | 要求者 |
| 20 | 已結束 *[access_package]* 的存取 | 這封電子郵件會在其存取權過期後傳送給終端使用者。 | 要求者 |

### <a name="access-request-emails"></a>存取要求電子郵件

當要求者針對設定為需要核准的存取套件提交存取要求時，新增至該原則的所有核准者都會收到電子郵件通知，其中包含要求的詳細資料。 電子郵件中的詳細資料包括：要求者的名稱組織和商業理由;和要求的存取開始和結束日期（如果有提供）。 詳細資料也會包含提交要求的時間，以及要求到期的時間。

此電子郵件包含連結核准者可以按一下以前往我的存取權核准或拒絕存取要求。 以下是傳送給第一個核准者或第二個核准者（如果已啟用2階段核准）來完成存取要求的電子郵件通知範例：

![核准存取封裝電子郵件的要求](./media/entitlement-management-shared/approver-request-email.png)

核准者也可以收到提醒電子郵件。 此電子郵件會要求核准者對要求做出決策。 以下是核准者收到的電子郵件通知範例，提醒他們採取行動：

![提醒存取要求電子郵件](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>替代核准者要求電子郵件

如果已啟用 [替代核准者] 設定，但要求仍處於擱置狀態，則會將它轉寄。 替代核准者將會收到一封電子郵件，以核准或拒絕要求。 您可以在第1階段和第2階段啟用替代核准者。 以下是替代核准者收到通知的電子郵件範例：

![替代核准者要求電子郵件](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

核准者和替代核准者都可以核准或拒絕要求。

### <a name="approved-or-denied-emails"></a>核准或拒絕的電子郵件

 當核准者收到要求者所提交的存取要求時，他們可以核准或拒絕存取要求。 核准者必須為他們的決策增加商業理由。 以下是核准要求之後，傳送給核准者和替代核准者的電子郵件範例：

![已核准存取封裝電子郵件的要求](./media/entitlement-management-process/approver-request-email-approved.png)

當存取要求獲得核准，並布建其存取權時，系統會將電子郵件通知傳送給要求者，他們現在可以存取存取套件。 以下是將存取套件授與存取權時，傳送給要求者的電子郵件通知範例：

![核准的要求者存取要求電子郵件](./media/entitlement-management-process/requestor-email-approved.png)

拒絕存取要求時，會傳送電子郵件通知給要求者。 以下是在拒絕存取要求時，傳送給要求者的電子郵件通知範例：

![要求者要求拒絕電子郵件](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2-階段核准存取要求電子郵件

如果已啟用2階段核准，則至少有兩個核准者必須核准要求（每個階段一個），然後要求者才能接收存取權。

在階段1期間，第一位核准者將會收到存取要求電子郵件並做出決定。 如果他們核准要求，第1階段中的所有前核准者和替代核准者（如果已啟用擴大）將會收到階段-1 已完成的通知。 以下是第1階段完成時所傳送通知的範例電子郵件：

![2階段存取要求電子郵件](./media/entitlement-management-process/approver-request-email-2stage.png)

第一或替代的核准者在階段1中核准要求之後，就會開始階段2。 在階段2期間，第二個核准者將會收到存取要求通知電子郵件。 第二次核准者或第2階段中的其他核准者（如果已啟用擴大）之後，決定核准或拒絕要求，通知電子郵件會傳送給第一個和第二個核准者，以及階段1和階段2中的所有替代核准者，以及要求者。

### <a name="expired-access-request-emails"></a>過期的存取要求電子郵件

如果沒有核准者核准或拒絕要求，存取要求可能會過期。 

當要求達到其設定的到期日且過期時，核准者就無法再核准或拒絕此要求。 以下是傳送給所有第一個、第二個（如果已啟用2階段核准）和替代核准者的通知電子郵件範例：

![核准者過期的存取要求電子郵件](./media/entitlement-management-process/approver-request-email-expired.png)

電子郵件通知也會傳送給要求者，通知他們其存取要求已過期，而且需要重新提交存取要求。 下圖顯示要求者的體驗，以及他們在要求延伸存取時所收到的電子郵件通知：

![要求者延伸存取程式流程](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

以下是當使用者的存取要求過期時，傳送給要求者的電子郵件通知範例：

![要求者過期的存取要求電子郵件](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)
