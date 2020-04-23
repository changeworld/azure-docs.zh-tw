---
title: Azure AD 中連續存取評估
description: 透過 Azure AD 中的連續存取評估更快的回應使用者狀態變更
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
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873299"
---
# <a name="continuous-access-evaluation"></a>連續存取評估

Microsoft 服務(如 Azure 活動目錄 (Azure AD) 和 Office 365)使用開放標準和協定來最大化互操作性。 其中最關鍵的是打開 ID 連接 (OIDC)。 當 Outlook 等用戶端應用程式連接到 Exchange Online 等服務時,API 請求將使用 OAuth 2.0 訪問權杖進行授權。 默認情況下,這些訪問權杖的有效期為一小時。 當用戶端過期時,用戶端將重定向回 Azure AD 以刷新它們。 這也提供了重新評估使用者存取策略的機會 - 我們可能選擇不刷新權杖,因為條件存取策略,或者因為使用者已在目錄中禁用。 

我們聽到來自客戶的大量反饋:由於重新應用條件訪問策略的訪問令牌存留期導致一小時的延遲,以及使用者狀態的更改(例如:由於毛茸茸而禁用)不夠好。

作為 OpenID 基金會[共用信號和事件](https://openid.net/wg/sse/)工作組的一部分,Microsoft 已提前參加了連續訪問評估協定 (CAEP) 計劃。 標識提供者和依賴方將能夠利用工作組定義的安全事件和訊號重新授權或終止存取。 這是一個令人興奮的工作,將提高許多平台和應用程序的安全性。

由於安全優勢如此之大,我們正在推出特定於 Microsoft 的初始實現,同時我們在標準機構內繼續工作。 當我們努力跨 Microsoft 服務部署這些連續訪問評估 (CAE) 功能時,我們學到了很多東西,並且正在與標準社區共用此資訊。 我們希望我們的部署經驗能夠有助於制定更好的行業標準,並承諾一旦批准該標準,即可實施該標準,從而使所有參與服務受益。

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE 在Microsoft服務中是如何工作的?

我們正在集中對 Exchange 和團隊進行持續訪問評估的初始實施。 我們希望將來能擴大對微軟其他服務的支援。 我們將開始僅對沒有條件訪問策略的租戶啟用連續訪問評估。 我們將利用我們在此階段從 CAE 中學到的知識,為正在進行的 CAE 推出提供資訊。

## <a name="service-side-requirements"></a>服務方要求

通過啟用服務(資源提供程式)訂閱 Azure AD 中的關鍵事件,以便可以近乎即時地評估和強制執行這些事件,從而實現連續訪問評估。 初始憑證中將強制執行以下事件:

- 使用者帳號被刪除或關閉
- 變更或重置使用者的密碼
- 管理員顯示式復原使用者的所有刷新權杖
- Azure AD 識別保護偵測到的升高的使用者風險

將來,我們希望添加更多事件,包括位置和設備狀態更改等事件。 **雖然我們的目標是強制是即時的,但在某些情況下,由於事件傳播時間,可能會觀察到長達 15 分鐘的延遲**。 

## <a name="client-side-claim-challenge"></a>用戶端索賠挑戰

在連續訪問評估之前,只要訪問權杖未過期,客戶端始終會嘗試從緩存中重播訪問權杖。 使用 CAE,我們引入了一個新案例,即資源提供程式可以拒絕令牌,即使令牌未過期也是如此。 為了通知客戶端繞過緩存,即使緩存的令牌尚未過期,我們引入了一種稱為**聲明質詢**的機制。 CAE 需要用戶端更新才能瞭解索賠挑戰。 以下支援索賠挑戰的最新版本:

- 視窗展望 
- Outlook iOS 
- 展望安卓 
- 展望 Mac 
- 視窗團隊
- 團隊 iOS 
- 團隊安卓 
- 團隊 Mac 

## <a name="token-lifetime"></a>權杖存留期

由於風險和策略是即時評估的,因此協商連續訪問評估感知會話的用戶端將依賴於 CAE 而不是現有的靜態訪問令牌存留期策略,這意味著協商 CAE 感知會話的啟用 CAE 的用戶端將不再遵守可配置的權杖存留期策略。

我們將在 CAE 會話中將訪問權杖存留期增加到 24 小時。 吊銷是由關鍵事件和策略評估驅動的,而不是任意的時間段。 此更改提高了應用程式的穩定性,而不會影響您的安全狀態。 

## <a name="example-flows"></a>範例流

### <a name="user-revocation-event-flow"></a>使用者撤銷事件串流:

![使用者撤銷事件串流](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支援 CAE 的用戶端向 AAD 提供認證或刷新權杖,要求為某些資源請求存取權杖。
1. 訪問權杖與其他專案一起返回到用戶端。
1. 管理員顯示式[復原使用者的所有刷新權杖](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 吊銷事件將從 Azure AD 發送到資源提供程式。
1. 向資源提供程序顯示訪問權杖。 資源提供程式評估權杖的有效性,並檢查使用者是否有任何吊銷事件。 資源提供程式使用此資訊決定是否授予對資源的訪問許可權。
1. 在這種情況下,資源提供者拒絕存取,並將 401+ 索賠質詢傳送回用戶端
1. 支援 CAE 的用戶端瞭解 401+ 索賠挑戰。 它繞過緩存並返回步驟 1,將其刷新令牌以及聲明質詢一起發送回 Azure AD。 然後,Azure AD 將重新評估所有條件,並提示使用者在這種情況下重新進行身份驗證。
 
## <a name="faqs"></a>常見問題集

### <a name="what-is-the-lifetime-of-my-access-token"></a>我的訪問權杖的存留期是多少?

如果您沒有使用支援 CAE 的用戶端,則預設訪問權杖生存期仍為 1 小時,除非您已使用[可配置權杖存留期 (CTL)](../develop/active-directory-configurable-token-lifetimes.md)預覽功能設定了存取權杖存留期。

如果您使用的是支援 CAE 的用戶端協商 CAE 感知會話,則存取權杖生存期的 CTL 設定將被覆寫,存取權杖存留期為 24 小時。

### <a name="how-quick-is-enforcement"></a>執行速度有多快?

雖然我們的目標是實現即時執行,但在某些情況下,由於事件傳播時間,可能會觀察到長達 15 分鐘的延遲。

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE 如何使用登錄頻率?

登錄頻率將遵循或沒有 CAE。

## <a name="next-steps"></a>後續步驟

[宣佈連續存取評估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
