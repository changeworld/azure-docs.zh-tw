---
title: 使用者遷移方法
titleSuffix: Azure AD B2C
description: 使用預先遷移或無縫遷移方法，將使用者帳戶從另一個識別提供者遷移至 Azure AD B2C。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fea8d93121fcb5622f86257febcc7d834b444098
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178801"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>將使用者遷移至 Azure AD B2C

從另一個識別提供者遷移至 Azure Active Directory B2C (Azure AD B2C) 可能也需要遷移現有的使用者帳戶。 這裡會討論兩個遷移方法， *預先遷移* 和 *流暢的遷移*。 無論使用哪一種方法，您都必須撰寫使用 [MICROSOFT GRAPH API](manage-user-accounts-graph-api.md) 的應用程式或腳本，以在 Azure AD B2C 中建立使用者帳戶。

## <a name="pre-migration"></a>預先遷移

在預先遷移流程中，您的遷移應用程式會針對每個使用者帳戶執行這些步驟：

1. 從舊的身分識別提供者讀取使用者帳戶，包括其目前的認證 (使用者名稱和密碼) 。
1. 使用目前的認證，在您的 Azure AD B2C 目錄中建立對應的帳戶。

在這兩種情況下，請使用預先遷移流程：

- 您可以存取使用者的純文字認證 (其使用者名稱和密碼) 。
- 認證會經過加密，但您可以將它們解密。

如需以程式設計方式建立使用者帳戶的詳細資訊，請參閱 [使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>無縫遷移

如果無法存取舊識別提供者中的純文字密碼，請使用無縫遷移流程。 例如，當：

- 密碼會以單向加密格式儲存，例如使用雜湊函數。
- 舊版身分識別提供者會以您無法存取的方式來儲存密碼。 例如，當身分識別提供者藉由呼叫 web 服務來驗證認證時。

無縫遷移流程仍需要預先遷移使用者帳戶，但是接著會使用 [自訂原則](custom-policy-get-started.md) 來查詢 [REST API](custom-policy-rest-api-intro.md) (您在第一次登入時建立) 來設定每個使用者的密碼。

無縫遷移流程因此有兩個階段： *預先遷移* 和 *設定認證*。

### <a name="phase-1-pre-migration"></a>階段1：預先遷移

1. 您的遷移應用程式會從舊的身分識別提供者讀取使用者帳戶。
1. 遷移應用程式會在您的 Azure AD B2C 目錄中建立對應的使用者帳戶，但不 *會設定密碼*。

### <a name="phase-2-set-credentials"></a>第2階段：設定認證

完成帳戶的遷移之後，您的自訂原則和 REST API 接著會在使用者登入時執行下列動作：

1. 讀取對應到所輸入電子郵件地址的 Azure AD B2C 使用者帳戶。
1. 藉由評估布林值延伸屬性來檢查帳戶是否已標示為要遷移。
    - 如果延伸模組屬性傳回 `True` ，請呼叫您的 REST API，以針對舊版身分識別提供者驗證密碼。
      - 如果 REST API 判斷密碼不正確，請將易記的錯誤傳回給使用者。
      - 如果 REST API 判斷密碼正確，請將密碼寫入 Azure AD B2C 帳戶，然後將 [布林值延伸模組] 屬性變更為 `False` 。
    - 如果布林值延伸屬性傳回 `False` ，請正常地繼續登入程式。

若要查看範例自訂原則和 REST API，請參閱 GitHub 上的 [無縫使用者遷移範例](https://aka.ms/b2c-account-seamless-migration) 。

![無縫遷移方法到使用者遷移的流程圖圖表](./media/user-migration/diagram-01-seamless-migration.png)<br />*圖表：無縫遷移流程*

## <a name="best-practices"></a>最佳作法

### <a name="security"></a>安全性

無縫遷移方法會使用您自己的自訂 REST API，以針對舊版身分識別提供者驗證使用者的認證。

**您必須保護 REST API 免受暴力密碼破解攻擊。** 攻擊者可以提交數個密碼，希望最終猜出使用者的認證。 若要協助抵禦這類攻擊，請在登入嘗試次數達到特定閾值時，停止將要求提供給您的 REST API。 此外，請確保 Azure AD B2C 與 REST API 之間的通訊安全。 若要了解如何保護您的 RESTful API 以用於實際執行環境，請參閱[保護 RESTful API](secure-rest-api.md)。

### <a name="user-attributes"></a>使用者屬性

並非舊版身分識別提供者中的所有資訊都應該遷移到您的 Azure AD B2C 目錄。 在遷移之前，請先識別要儲存在 Azure AD B2C 中的適當使用者屬性集。

- Azure AD B2C 中 **的商店**
  - 使用者名稱、密碼、電子郵件地址、電話號碼、成員資格號碼/識別碼。
  - 隱私權原則和使用者授權合約的同意標記。
- **請勿** 儲存在 Azure AD B2C
  - 機密資料，例如信用卡號碼、社會安全號碼 (SSN) 、醫療記錄，或政府或產業合規性主體所管制的其他資料。
  - 行銷或通訊喜好設定、使用者行為和見解。

### <a name="directory-clean-up"></a>清除目錄

開始進行遷移程式之前，請先有機會清除您的目錄。

- 識別要儲存在 Azure AD B2C 中的使用者屬性集合，並只遷移您需要的內容。 如有必要，您可以建立 [自訂屬性](user-flow-custom-attributes.md) ，以儲存更多有關使用者的資料。
- 如果您是從具有多個驗證來源的環境進行遷移 (例如，每個應用程式都有自己的使用者目錄) ，請遷移至 Azure AD B2C 中的整合帳戶。
- 如果多個應用程式有不同的使用者名稱，您可以使用身分識別集合，將所有這些應用程式儲存在 Azure AD B2C 使用者帳戶中。 在密碼方面，讓使用者選擇其中一個，並在目錄中設定。 例如，在無縫式遷移的情況下，只應該將所選的密碼儲存在 Azure AD B2C 帳戶中。
- 請在遷移前移除未使用的使用者帳戶，或不要遷移過時的帳戶。

### <a name="password-policy"></a>密碼原則

如果您要遷移的帳戶的密碼強度比 Azure AD B2C 強制執行的 [強式密碼強度](../active-directory/authentication/concept-sspr-policy.md) 弱，您可以停用強式密碼需求。 如需詳細資訊，請參閱 [密碼原則屬性](user-profile-attributes.md#password-policy-attribute)。

## <a name="next-steps"></a>下一步

GitHub 上的 [azure ad-b2c/使用者-遷移](https://github.com/azure-ad-b2c/user-migration) 存放庫包含無縫遷移自訂原則範例和 REST API 程式碼範例：

[無縫的使用者遷移自訂原則 & REST API 程式碼範例](https://aka.ms/b2c-account-seamless-migration)
