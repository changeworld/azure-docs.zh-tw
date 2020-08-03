---
title: Azure Active Directory B2C 中的使用者流程版本 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中可用的使用者流程版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67949c31c710d88a05e1e110860fe703caf66d04
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481320"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的使用者流程版本

Azure Active Directory B2C （Azure AD B2C）中的使用者流程，可協助您設定完整描述客戶身分識別體驗的一般[原則](user-flow-overview.md)。 這些體驗包括註冊、登入、密碼重設或設定檔編輯。 下表描述 Azure AD B2C 中可用的使用者流程。

> [!IMPORTANT]
> 我們改變了我們參考使用者流程版本的方式。 先前，我們提供了 V1 （已準備好用於生產環境）的版本，以及 v1.1 和 V2 （預覽）版本。 現在，我們已將使用者流程合併為兩個版本：
>
>- **建議**的使用者流程是使用者流程的新預覽版本。 它們經過徹底測試，並結合了舊版**V2**和**v1.1**版本的所有功能。 未來，將會維護並更新新的建議使用者流程。 當您移至這些新的建議使用者流程後，就可以在新功能發行時存取它們。
>- **標準**使用者流程，先前稱為**V1**，已正式運作，可供生產環境使用的使用者流程。 如果您的使用者流程是任務關鍵性的，而且取決於高穩定的版本，您可以繼續使用標準使用者流程，並意識到這些版本將不會進行維護和更新。
>
>所有舊版預覽使用者流程（v1.1 和 V2）都是在**2021 年8月1日**淘汰的路徑上。 可能的話，我們強烈建議您儘快[切換至新的**建議**版本](#how-to-switch-to-a-new-recommended-user-flow)，讓您隨時都能利用最新的功能和更新。 *這些變更僅適用于 Azure 公用雲端。其他環境將繼續使用[舊版使用者流程版本](user-flow-versions-legacy.md)設定。*

## <a name="recommended-user-flows"></a>建議的使用者流程

建議的使用者流程是將新功能與舊版 V2 和 v1.1 功能結合的預覽版本。 未來，將會維護並更新建議的使用者流程。

| 使用者流程 | 說明 |
| --------- | ----------- |
| 密碼重設（預覽） | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯（預覽） | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入（預覽） | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>登入頁面自訂</li></ul> |
| 註冊（預覽） | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 註冊和登入（預覽） | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>標準使用者流程

標準使用者流程（先前稱為 V1）已正式運作，可供生產環境使用的使用者流程。 標準使用者流程將不會繼續更新。

| 使用者流程 | 說明 |
| --------- | ----------- | ----------- |
| 密碼重設 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 設定檔編輯 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>封鎖登入</li><li>強制密碼重設</li><li>讓我保持登入 (KMSI)</ul><br>您無法自訂此使用者流程的使用者介面與。 |
| 註冊 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul> |
| 註冊與登入 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](custom-policy-multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](user-flow-password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>如何切換至新的建議使用者流程

若要從舊版使用者流程切換到新**建議**的預覽版本，請遵循下列步驟：

1. 遵循[教學課程：在 Azure Active Directory 中建立使用者流程](tutorial-create-user-flows.md)中的步驟，建立新的使用者流程原則。 建立使用者流程時，請選取**建議**的版本。

3. 使用舊版原則中所設定的相同設定來設定新的使用者流程。

4. 將您的應用程式登入 URL 更新為新建立的原則。

5. 在測試使用者流程並確認其運作正常之後，請遵循下列步驟來刪除舊版使用者流程：
   1. 在 [Azure AD B2C 租使用者總覽] 功能表中，選取 [**使用者流程**]。
   2. 尋找您要刪除的使用者流程。
   3. 在最後一個資料行中，選取內容功能表（**...**），然後選取 [**刪除**]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>我仍然可以建立舊版 V2 和 v1.1 使用者流程嗎？

您將無法根據舊版 V2 和 v1.1 版本來建立新的使用者流程，但您可以繼續讀取、更新及刪除目前使用的任何舊版 V2 和 v1.1 使用者流程。

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>是否有任何理由要繼續使用舊版 V2 和 v1.1 使用者流程？

不完全是。 新**建議**的預覽版本包含與舊版 V2 和 v1.1 版本相同的功能。 未移除任何內容，事實上，它們現在包含額外的功能。

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>如果我沒有從舊版 V2 和 v1.1 原則切換，它會如何影響我的應用程式？

如果您使用舊版 V2 或 v1.1 使用者流程，您的應用程式將不會受到此版本設定變更的影響。 但若要取得新功能或原則變更的存取權，您必須切換至新的**建議**版本。

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft 仍然會支援我的舊版 V2 或 v1.1 使用者流程原則嗎？

舊版 V2 和1.1 版的使用者流程將繼續受到完整的支援。
