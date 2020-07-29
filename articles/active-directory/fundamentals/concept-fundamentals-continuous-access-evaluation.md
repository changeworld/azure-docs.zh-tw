---
title: Azure AD 中的持續存取評估
description: 使用 Azure AD 中的持續存取評估，更快地回應使用者狀態的變更
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0cb402741163c657b3e7961eb5a4f9c8e18dafd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673015"
---
# <a name="continuous-access-evaluation"></a>持續存取評估

Microsoft 服務（如 Azure Active Directory （Azure AD）和 Office 365）使用開放式標準和通訊協定，將互通性最大化。 其中一個最重要的是 Open ID Connect （OIDC）。 當 Outlook 之類的用戶端應用程式連接到 Exchange Online 之類的服務時，API 要求會使用 OAuth 2.0 存取權杖進行授權。 根據預設，這些存取權杖的有效時間為一小時。 當它們過期時，用戶端會重新導向回到 Azure AD 以重新整理。 這也有機會重新評估使用者存取原則-我們可能因為條件式存取原則而選擇不要重新整理權杖，或因為該使用者已在目錄中停用。 

權杖到期和重新整理是產業中的標準機制。 話雖如此，客戶對於使用者的風險狀況變更（例如：從公司辦公室移到當地咖啡廳，或在黑色市場上探索到的使用者認證）之間的延遲有一些疑慮，而且可以強制執行與該變更相關的原則。 我們已經實驗了降低權杖存留期的「鈍物件」方法，但發現它們會降低使用者經驗和可靠性，而不會消除風險。

及時回應原則違規或安全性問題，確實需要權杖簽發者（例如 Azure AD）與信賴憑證者（例如 Exchange Online）之間的「交談」。 這個雙向交談提供兩個重要功能。 信賴憑證者可以注意到專案已變更，例如來自新位置的用戶端，以及告訴權杖簽發者。 它也會提供權杖簽發者一種方法，告訴信賴憑證者因帳戶遭到入侵、停用或其他考慮，而停止尊重指定使用者的權杖。 此交談的機制是持續存取評估（CAE）。

Microsoft 曾成為連續存取評估通訊協定（CAEP）計畫的早期參與者，做為 OpenID Foundation 的[共用信號和事件](https://openid.net/wg/sse/)工作群組的一部分。 身分識別提供者和信賴憑證者將能夠利用工作群組所定義的安全性事件和信號來重新授權或終止存取權。 這是一項令人興奮的工作，可提升許多平臺和應用程式的安全性。

因為安全性優勢非常好用，所以我們會在標準內建的持續工作中，以平行方式推出 Microsoft 特有的初始實施。 當我們在 Microsoft 服務中部署這些持續存取評估（CAE）功能時，我們學到很多，而且與標準社區分享這項資訊。 我們希望我們的部署經驗可以協助通知更棒的產業標準，並承諾在核准的情況下實施該標準，讓所有參與的服務都能受益。

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE 在 Microsoft 服務中的運作方式為何？

我們將焦點放在對 Exchange 和小組進行持續存取評估的初始過程中。 我們希望未來將支援擴充至其他 Microsoft 服務。 我們只會開始針對沒有條件式存取原則的租使用者啟用連續存取評估。 我們將從這個 CAE 階段使用我們的學習，以通知我們持續推出的 CAE。

## <a name="service-side-requirements"></a>服務端需求

持續存取評估的實行方式是讓服務（資源提供者）訂閱 Azure AD 中的重大事件，讓這些事件可以近乎即時地進行評估和強制執行。 此初始 CAE 首度發行將會強制執行下列事件：

- 已刪除或停用使用者帳戶
- 使用者的密碼已變更或重設
- 已為使用者啟用 MFA
- 管理員明確撤銷使用者的所有重新整理權杖
- Azure AD Identity Protection 偵測到提高許可權的使用者風險

未來，我們希望新增更多事件，包括位置和裝置狀態變更等事件。 **雖然我們的目標是要立即強制執行，但在某些情況下，可能會因為事件傳播時間而發現最多15分鐘的延遲**。 

## <a name="client-side-claim-challenge"></a>用戶端索賠挑戰

在進行連續存取評估之前，用戶端一律會嘗試從其快取中重新執行存取權杖，但前提是它未過期。 透過 CAE，我們引進了新的案例，讓資源提供者可以拒絕權杖，即使尚未過期也一樣。 為了通知用戶端即使快取的權杖尚未過期，也會略過其快取，我們引進了一項稱為「宣告**挑戰**」的機制。 CAE 需要用戶端更新以瞭解宣告挑戰。 下列應用程式的最新版本支援索賠挑戰：

- 適用于 Windows 的 Outlook 
- 適用于 iOS 的 Outlook 
- 適用于 Android 的 Outlook 
- Outlook for Mac 
- Windows 團隊
- 適用于 iOS 的小組 
- 適用于 Android 的小組 
- Mac 團隊 

## <a name="token-lifetime"></a>權杖存留期

因為風險和原則會即時進行評估，所以協商連續存取評估感知會話的用戶端會依賴 CAE，而不是現有的靜態存取權杖存留期原則，這表示協調 CAE 感知會話的支援 CAE 的用戶端將不再接受可設定的權杖存留期原則。

在 CAE 會話中，我們會將存取權杖存留期增加為24小時。 撤銷是由重大事件和原則評估所驅動，而不是任意時間週期。 這項變更會提高應用程式的穩定性，而不會影響您的安全性狀態。 

## <a name="example-flows"></a>範例流程

### <a name="user-revocation-event-flow"></a>使用者撤銷事件流程：

![使用者撤銷事件流程](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. 具有 CAE 功能的用戶端會向 AAD 出示認證或重新整理權杖，以要求某些資源的存取權杖。
1. 存取權杖會連同其他成品一起傳回給用戶端。
1. 系統管理員明確[撤銷使用者的所有](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)重新整理權杖。 撤銷事件將會從 Azure AD 傳送給資源提供者。
1. 存取權杖會呈現給資源提供者。 資源提供者會評估權杖的有效性，並檢查使用者是否有任何撤銷事件。 資源提供者會使用此資訊來決定是否要授與資源的存取權。
1. 在此情況下，資源提供者會拒絕存取，並將 401 + 宣告挑戰傳送回用戶端
1. 支援 CAE 的用戶端瞭解 401 + 宣告挑戰。 它會略過快取，並返回步驟1，將其重新整理權杖連同宣告挑戰傳回 Azure AD。 Azure AD 接著會重新評估所有條件，並在此情況下提示使用者重新驗證。

## <a name="faqs"></a>常見問題集

### <a name="what-is-the-lifetime-of-my-access-token"></a>我的存取權杖存留期為何？

如果您不是使用支援 CAE 的用戶端，除非您已使用可設定的[權杖存留期（CTL）](../develop/active-directory-configurable-token-lifetimes.md)預覽功能來設定存取權杖存留期，否則預設存取權杖存留期仍然會是1小時。

如果您使用可支援 CAE 的用戶端來協調 CAE 感知會話，將會覆寫存取權杖存留期的 CTL 設定，而且存取權杖存留期會是24小時。

### <a name="how-quick-is-enforcement"></a>強制執行的速度如何？

雖然我們的目標是要立即強制執行，但在某些情況下，可能會因為事件傳播時間而發現最多15分鐘的延遲。

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE 如何使用登入頻率？

登入頻率將會接受或不含 CAE。

## <a name="next-steps"></a>後續步驟

[宣佈持續存取評估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
