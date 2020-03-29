---
title: Azure 活動目錄 B2B 最佳實踐和建議
description: 在 Azure 活動目錄中瞭解企業對企業 （B2B） 來賓使用者訪問的最佳做法和建議。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050854"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure 活動目錄 B2B 最佳實踐
本文包含 Azure 活動目錄 （Azure AD） 中企業對企業 （B2B） 協作的建議和最佳實踐。

   > [!IMPORTANT]
   > **從 2021 年 3 月 31 日起**，Microsoft 將不再支援為 B2B 協作方案創建非託管 Azure AD 帳戶和租戶來兌換邀請。 在準備中，我們鼓勵客戶選擇[電子郵件一次性密碼身份驗證](one-time-passcode.md)。 我們歡迎您對此公共預覽功能的回饋，並興奮地創建更多協作方式。

## <a name="b2b-recommendations"></a>B2B 建議
| 建議 | 註解 |
| --- | --- |
| 為了獲得最佳的登錄體驗，請與標識提供程式聯合 | 盡可能直接與標識提供程式聯合，允許受邀使用者登錄到共用應用和資源，而無需創建 Microsoft 帳戶 （MSA） 或 Azure AD 帳戶。 您可以使用 Google[聯合功能](google-federation.md)允許 B2B 來賓使用者使用其 Google 帳戶登錄。 或者，您可以使用 Direct[聯合（預覽）功能](direct-federation.md)與標識提供程式 （IdP） 支援 SAML 2.0 或 WS-Fed 協定的任何組織建立直接聯合。 |
| 為無法通過其他方式進行身份驗證的 B2B 來賓使用電子郵件一次性密碼（預覽）功能 | [電子郵件一次性密碼（預覽）](one-time-passcode.md)功能對 B2B 來賓使用者進行身份驗證，當他們無法通過其他方式（如 Azure AD、Microsoft 帳戶 （MSA） 或 Google 聯合）進行身份驗證時。 當來賓使用者兌換邀請或存取共用資源時，他們可以要求一個暫時性驗證碼，此驗證碼會傳送到他們的電子郵件地址。 之後，他們便可輸入此驗證碼繼續登入。 |
| 將公司商標新增至登入頁面 | 您可以自訂登錄頁面，以便 B2B 來賓使用者更加直觀。 瞭解如何[添加公司品牌以登錄和訪問面板頁面](../fundamentals/customize-branding.md)。 |
| 將您的隱私權聲明添加到 B2B 來賓使用者兌換體驗 | 您可以將組織隱私權聲明的 URL 添加到首次邀請兌換流程，以便受邀使用者必須同意您的隱私條款才能繼續。 請參閱["如何"：在 Azure 活動目錄中添加組織的隱私資訊](https://aka.ms/adprivacystatement)。 |
| 使用批量邀請（預覽）功能同時邀請多個 B2B 來賓使用者 | 使用 Azure 門戶中的批量邀請預覽功能同時邀請多個來賓使用者加入您的組織。 此功能允許您上傳 CSV 檔以創建 B2B 來賓使用者並批量發送邀請。 有關[批量邀請 B2B 使用者的教程](tutorial-bulk-invite.md)，請參閱教程。 |
| 為多重要素驗證 （MFA） 強制實施條件訪問策略 | 我們建議在要與合作夥伴 B2B 使用者共用的應用上強制實施 MFA 策略。 這樣，無論合作夥伴組織是否使用 MFA，MFA 都將在租戶中的應用上持續強制執行。 請參閱[B2B 協作使用者的條件訪問](conditional-access.md)。 |
| 如果要強制實施基於設備的條件訪問策略，請使用排除清單來允許訪問 B2B 使用者 | 如果組織中啟用了基於設備的條件訪問策略，則 B2B 來賓使用者設備將被阻止，因為它們不受組織管理。 您可以創建包含特定合作夥伴使用者的排除清單，以將其從基於設備的條件訪問策略中排除。 請參閱[B2B 協作使用者的條件訪問](conditional-access.md)。 |
| 提供指向 B2B 來賓使用者的直接連結時，請使用特定于租戶的 URL | 作為邀請電子郵件的替代方法，您可以向來賓提供指向應用或閘戶的直接連結。 此直接連結必須特定于租戶，這意味著它必須包含租戶 ID 或已驗證的域，以便可以在共用應用所在的租戶中對來賓進行身份驗證。 請參閱[來賓使用者的兌換體驗](redemption-experience.md)。 |
| 開發應用時，請使用 UserType 來確定來賓使用者體驗  | 如果您正在開發應用程式，並且希望為租戶使用者和來賓使用者提供不同的體驗，請使用 UserType 屬性。 權杖中目前不包含 UserType 宣告。 應用程式應使用 Microsoft 圖形 API 查詢目錄，以便使用者獲取其 UserType。 |
| 僅當使用者與組織的關係發生更改時，*才*更改 UserType 屬性 | 儘管可以使用 PowerShell 將使用者的 UserType 屬性從"成員"轉換為來賓（反之亦然），但僅當使用者與組織的關係發生更改時，才應更改此屬性。 請參閱[B2B 來賓使用者的屬性](user-properties.md)。|

## <a name="next-steps"></a>後續步驟

[管理 B2B 共用](delegate-invitations.md)
