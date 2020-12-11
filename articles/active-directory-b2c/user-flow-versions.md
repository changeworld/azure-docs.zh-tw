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
ms.openlocfilehash: 75ad3743a90f5773163a8f115e1924b8c5bbe097
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108088"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的使用者流程版本

Azure Active Directory B2C (Azure AD B2C) 中的使用者流程，可協助您設定可完整描述客戶身分識別體驗的一般 [原則](user-flow-overview.md) 。 這些體驗包括註冊、登入、密碼重設或設定檔編輯。 下表說明 Azure AD B2C 中可用的使用者流程。

> [!IMPORTANT]
> 我們已變更使用者流程版本的參考方式。 以前，我們提供了 V1 (可用於生產環境) 版本，以及 V1.1 和 V2 (預覽) 版本。 現在，我們已將使用者流程合併為兩個版本：
>
>- **建議** 的使用者流程是新的使用者流程預覽版本。 它們經過徹底的測試，並結合舊版 **V2** 和 **v1.1** 版本的所有功能。 未來，將會維護和更新新的建議使用者流程。 一旦您移至這些新的建議使用者流程之後，您就可以在發行時存取新功能。
>- **標準** 使用者流程（之前稱為 **V1**）已正式推出，可供生產環境使用的使用者流程。 如果您的使用者流程是任務關鍵性的，且相依于高度穩定的版本，您就可以繼續使用標準使用者流程，並意識到這些版本不會進行維護和更新。
>
>所有舊版預覽使用者流程 (v1.1 和 V2) 都是在 **2021 年8月1日** 淘汰的路徑上。 可能的話，強烈建議您儘快 [切換至新的 **建議** 版本](#how-to-switch-to-a-new-recommended-user-flow) ，讓您隨時都能利用最新的功能和更新。 *這些變更僅適用于 Azure 公用雲端。其他環境將繼續使用 [舊版的使用者流程版本控制](user-flow-versions-legacy.md)。*

## <a name="recommended-user-flows"></a>建議的使用者流程

建議的使用者流程是預覽版本，其結合了舊版 V2 和 v1.1 功能的新功能。 未來，建議的使用者流程將會進行維護和更新。

| 使用者流程 | 說明 |
| --------- | ----------- |
| 密碼重設 (預覽)  | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](password-complexity.md)</li></ul> |
| 設定檔編輯 (預覽)  | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入 (預覽版)  | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>登入頁面自訂</li></ul> |
| 註冊 (預覽)  | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](password-complexity.md)</li></ul> |
| 註冊並登入 (預覽版)  | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[年齡管制](basic-age-gating.md)</li><li>[密碼複雜性需求](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>標準使用者流程

之前稱為 V1)  (標準使用者流程已正式推出，可供生產環境使用的使用者流程。 標準使用者流程將不會往後更新。

| 使用者流程 | 說明 |
| --------- | ----------- | ----------- |
| 密碼重設 | 讓使用者能夠在驗證電子郵件後選擇新密碼。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>權杖相容性設定</li><li>[密碼複雜性需求](password-complexity.md)</li></ul> |
| 設定檔編輯 | 讓使用者能夠設定其使用者屬性。 使用此使用者流程，您可以設定： <ul><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li></ul> |
| 登入 | 讓使用者帳戶能夠登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>封鎖登入</li><li>強制密碼重設</li><li>讓我保持登入 (KMSI)</ul><br>您無法自訂此使用者流程的使用者介面與。 |
| 註冊 | 讓使用者能夠建立帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](password-complexity.md)</li></ul> |
| 註冊與登入 | 讓使用者帳戶能夠建立帳戶或登入其帳戶。 使用此使用者流程，您可以設定： <ul><li>[多重要素驗證](multi-factor-authentication.md)</li><li>[權杖存留期](tokens-overview.md)</li><li>權杖相容性設定</li><li>工作階段行為</li><li>[密碼複雜性需求](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>如何切換至新的建議使用者流程

若要從舊版的使用者流程切換至新的 **建議** 預覽版本，請遵循下列步驟：

1. 遵循 [教學課程：在 Azure Active Directory 中建立使用者流程中](tutorial-create-user-flows.md)的步驟，建立新的使用者流程原則。 建立使用者流程時，請選取 **建議** 的版本。

3. 使用舊版原則中設定的相同設定來設定新的使用者流程。

4. 將您的應用程式登入 URL 更新為新建立的原則。

5. 測試使用者流程並確認其正常運作之後，請遵循下列步驟來刪除舊版使用者流程：
   1. 在 Azure AD B2C 租使用者總覽] 功能表中，選取 [ **使用者流程**]。
   2. 尋找您想要刪除的使用者流程。
   3. 在最後一個資料行中，選取內容功能表 (**...**) 然後選取 [ **刪除**]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>我是否仍然可以建立舊版 V2 和 v1.1 使用者流程？

您將無法根據舊版 V2 和 v1.1 版本來建立新的使用者流程，但您可以繼續閱讀、更新及刪除目前使用的任何舊版 V2 和 v1.1 使用者流程。

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>是否有任何理由繼續使用舊版 V2 和 v1.1 使用者流程？

不完全是。 新 **建議** 的預覽版本包含與舊版 V2 和1.1 版相同的功能。 未移除任何專案，事實上它們現在包含其他功能。

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>如果我沒有從舊版 V2 和 v1.1 原則切換，會如何影響我的應用程式？

如果您使用舊版 V2 或 v1.1 使用者流程，則您的應用程式不會受到此版本控制變更的影響。 但是若要存取新功能或原則變更，您必須切換至新的 **建議** 版本。

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft 是否仍支援舊版 V2 或 v1.1 使用者流程原則？

舊版 V2 和1.1 版的使用者流程將繼續受到完整支援。
