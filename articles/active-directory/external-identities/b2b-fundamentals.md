---
title: Azure Active Directory B2B 的最佳作法和建議
description: 瞭解 Azure Active Directory 中的企業對企業 (B2B) 來賓使用者存取的最佳作法和建議。
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
ms.openlocfilehash: b87881ad5533724f08de3b2f348d1487f763ab04
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442162"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 最佳做法
本文包含 Azure Active Directory (Azure AD) 中的企業對企業 (B2B) 共同作業的建議和最佳作法。

   > [!IMPORTANT]
   > **自 2021 年 3 月 31 日起**，Microsoft 將不再支援兌換邀請，而是建立適用於 B2B 共同作業案例的非受控 Azure AD 帳戶和租用戶。 在準備過程中，我們鼓勵客戶選擇使用[電子郵件一次性密碼驗證](one-time-passcode.md)。 我們歡迎您提供此公開預覽功能的意見反應，而且期待能建立更多共同作業的方式。

## <a name="b2b-recommendations"></a>B2B 建議
| 建議 | 註解 |
| --- | --- |
| 為了獲得最佳的登入體驗，與身分識別提供者聯盟 | 可能的話，請直接與身分識別提供者同盟，讓受邀的使用者能夠登入您的共用應用程式和資源，而不需要建立 Microsoft 帳戶 (Msa) 或 Azure AD 帳戶。 您可以使用 [google 同盟功能](google-federation.md) ，讓 B2B 來賓使用者使用其 Google 帳戶登入。 或者，您可以使用 [直接同盟 (預覽版) 功能](direct-federation.md) ，設定與任何組織（其身分識別提供者 (IdP) 支援 SAML 2.0 或 WS-Fed 通訊協定）的直接同盟。 |
| 針對無法透過其他方式進行驗證的 B2B 來賓，使用電子郵件單次密碼 (預覽) 功能 | [電子郵件單次密碼 (預覽) ](one-time-passcode.md)功能無法透過其他方式（例如 Azure AD、MICROSOFT 帳戶 (MSA) 或 Google 同盟）進行驗證時，驗證 B2B 來賓使用者。 當來賓使用者兌換邀請或存取共用資源時，他們可以要求一個暫時性驗證碼，此驗證碼會傳送到他們的電子郵件地址。 之後，他們便可輸入此驗證碼繼續登入。 |
| 將公司商標新增至登入頁面 | 您可以自訂登入頁面，讓 B2B 來賓使用者更直覺化。 瞭解如何 [將公司商標新增至登入和存取面板頁面](../fundamentals/customize-branding.md)。 |
| 將您的隱私權聲明新增至 B2B 來賓使用者兌換體驗 | 您可以將組織隱私權聲明的 URL 新增到第一次邀請兌換程式，讓受邀的使用者必須同意您的隱私權條款才能繼續。 請參閱 [如何：在 Azure Active Directory 中新增貴組織的隱私權資訊](../fundamentals/active-directory-properties-area.md)。 |
| 使用大量邀請 (預覽) 功能，同時邀請多位 B2B 來賓使用者 | 使用 Azure 入口網站中的大量邀請預覽功能，同時邀請多位來賓使用者加入您的組織。 這項功能可讓您上傳 CSV 檔案以建立 B2B 來賓使用者，並大量傳送邀請。 請參閱 [大量邀請 B2B 使用者的教學](tutorial-bulk-invite.md)課程。 |
| 針對 Multi-Factor Authentication (MFA) 強制執行條件式存取原則 | 建議您在想要與合作夥伴 B2B 使用者共用的應用程式上，強制執行 MFA 原則。 如此一來，不論夥伴組織是否使用 MFA，MFA 都會一致地在您的租使用者中的應用程式上強制執行。 查看 [B2B 共同作業使用者的條件式存取](conditional-access.md)。 |
| 如果您要強制以裝置為基礎的條件式存取原則，請使用排除清單以允許存取 B2B 使用者 | 如果您的組織已啟用以裝置為基礎的條件式存取原則，將會封鎖 B2B 來賓使用者裝置，因為這些裝置不是由您的組織管理。 您可以建立包含特定合作夥伴使用者的排除清單，以將其從裝置型條件式存取原則中排除。 查看 [B2B 共同作業使用者的條件式存取](conditional-access.md)。 |
| 提供 B2B 來賓使用者的直接連結時，請使用租使用者特定的 URL | 除了邀請電子郵件以外，您也可以為來賓提供應用程式或入口網站的直接連結。 此直接連結必須是租使用者特定的，這表示它必須包含租使用者識別碼或已驗證的網域，才能在您的租使用者中驗證可共用應用程式所在的來賓。 請參閱 [來賓使用者的兌換體驗](redemption-experience.md)。 |
| 開發應用程式時，請使用 UserType 來判斷來賓使用者體驗  | 如果您要開發應用程式，而且想要為租使用者使用者和來賓使用者提供不同的體驗，請使用 UserType 屬性。 權杖中目前不包含 UserType 宣告。 應用程式應該使用 Microsoft Graph API 來查詢目錄，讓使用者取得其 UserType。 |
| 只有在使用者與組織的關聯性變更時， *才* 變更 UserType 屬性 | 雖然您可以使用 PowerShell 將使用者的 UserType 屬性從成員轉換為 Guest (反之亦然) ，但只有在使用者與組織的關聯性變更時，才應該變更此屬性。 請參閱 [B2B 來賓使用者的屬性](user-properties.md)。|

## <a name="next-steps"></a>後續步驟

[管理 B2B 共用](delegate-invitations.md)