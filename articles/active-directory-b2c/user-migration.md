---
title: 使用者遷移方法
titleSuffix: Azure AD B2C
description: 使用大量匯入或無縫遷移方法，將使用者帳戶從另一個識別提供者遷移到 Azure AD B2C。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332338"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>將使用者遷移至 Azure AD B2C

從另一個識別提供者遷移至 Azure Active Directory B2C （Azure AD B2C）可能也需要遷移現有的使用者帳戶。 這裡討論兩種遷移方法：*大量匯入*和*順暢的遷移*。 使用任一種方法時，您都必須撰寫應用程式或腳本，以使用[MICROSOFT GRAPH API](manage-user-accounts-graph-api.md)在 Azure AD B2C 中建立使用者帳戶。

## <a name="bulk-import"></a>大量匯入

在大量匯入流程中，您的遷移應用程式會針對每個使用者帳戶執行下列步驟：

1. 從舊的識別提供者讀取使用者帳戶，包括其目前的認證（使用者名稱和密碼）。
1. 在您的 Azure AD B2C 目錄中，使用目前的認證建立對應的帳戶。

在這兩種情況下，請使用大量匯入流程：

- 您可以存取使用者的純文字認證（其使用者名稱和密碼）。
- 認證會經過加密，但您可以將其解密。

如需以程式設計方式建立使用者帳戶的相關資訊，請參閱[使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>順暢的遷移

如果無法存取舊識別提供者中的純文字密碼，請使用順暢的遷移流程。 例如，when：

- 密碼會以單向加密格式儲存，例如使用雜湊函數。
- 舊版識別提供者會以您無法存取的方式儲存密碼。 例如，當識別提供者藉由呼叫 web 服務來驗證認證時。

順暢的遷移流程仍然需要大量遷移使用者帳戶，但接著會使用[自訂原則](custom-policy-get-started.md)來查詢[REST API](custom-policy-rest-api-intro.md) （您建立的），以便在第一次登入時設定每位使用者的密碼。

因此，順暢的遷移流程有兩個階段：*大量匯入*和*設定認證*。

### <a name="phase-1-bulk-import"></a>第1階段：大量匯入

1. 您的遷移應用程式會從舊的識別提供者讀取使用者帳戶。
1. 遷移應用程式會在您的 Azure AD B2C 目錄中建立對應的使用者帳戶，但不*會設定密碼*。

### <a name="phase-2-set-credentials"></a>第2階段：設定認證

完成帳戶的大量遷移之後，您的自訂原則和 REST API 接著會在使用者登入時執行下列動作：

1. 閱讀與輸入的電子郵件地址對應的 Azure AD B2C 使用者帳戶。
1. 藉由評估布林值延伸屬性，檢查帳戶是否標示為可供遷移。
    - 如果延伸模組屬性傳回`True`，請呼叫您的 REST API，以針對舊版識別提供者驗證密碼。
      - 如果 REST API 判斷密碼不正確，就會向使用者傳回易記的錯誤。
      - 如果 REST API 判斷密碼是否正確，請將密碼寫入 Azure AD B2C 帳戶，並將布林值延伸屬性變更為`False`。
    - 如果布林值延伸屬性傳回`False`，請照常繼續登入程式。

若要查看範例自訂原則和 REST API，請參閱 GitHub 上的[順暢使用者遷移範例](https://aka.ms/b2c-account-seamless-migration)。

![使用者遷移的順暢遷移方法流程圖](./media/user-migration/diagram-01-seamless-migration.png)<br />*圖表：順暢的遷移流程*

## <a name="best-practices"></a>最佳作法

### <a name="security"></a>安全性

順暢的遷移方法會使用您自己的自訂 REST API，對舊版識別提供者驗證使用者的認證。

**您必須保護 REST API 不受暴力密碼破解攻擊。** 攻擊者可以提交數個密碼，希望最終猜出使用者的認證。 若要協助對抗這類攻擊，請在登入嘗試次數達到特定臨界值時，停止提供 REST API 的要求。 此外，請保護 Azure AD B2C 和您 REST API 之間的通訊。 若要瞭解如何保護您的 RESTful Api 以用於生產環境，請參閱[保護 RESTFUL api](secure-rest-api.md)。

### <a name="user-attributes"></a>使用者屬性

不是舊版識別提供者中的所有資訊都應該遷移至您的 Azure AD B2C 目錄。 在遷移之前，識別要在 Azure AD B2C 中儲存的適當使用者屬性集合。

- 在 Azure AD B2C 中**執行**存放區
  - 使用者名稱、密碼、電子郵件地址、電話號碼、成員資格編號/識別碼。
  - 隱私權原則和使用者授權合約的同意標記。
- **不要**儲存在 Azure AD B2C
  - 敏感性資料，例如信用卡號碼、社會安全號碼（SSN）、醫療記錄，或其他由政府或產業合規性主體所管制的資料。
  - 行銷或溝通喜好設定、使用者行為和見解。

### <a name="directory-clean-up"></a>清理目錄

開始進行遷移程式之前，請先花機會清理您的目錄。

- 識別要在 Azure AD B2C 中儲存的一組使用者屬性，並只遷移您需要的內容。 如有需要，您可以建立[自訂屬性](custom-policy-custom-attributes.md)來儲存使用者的更多相關資料。
- 如果您要從具有多個驗證來源的環境進行遷移（例如，每個應用程式都有自己的使用者目錄），請在 Azure AD B2C 中遷移至統一的帳戶。
- 如果多個應用程式有不同的使用者名稱，您可以使用身分識別集合，將它們全部儲存在 Azure AD B2C 的使用者帳戶中。 至於密碼，請讓使用者選擇其中一個，並將它設定在目錄中。 例如，透過順暢的遷移，只有所選的密碼才會儲存在 Azure AD B2C 帳戶中。
- 請先移除未使用的使用者帳戶再進行遷移，或不要遷移過時的帳戶。

### <a name="password-policy"></a>密碼原則

如果您要遷移的帳戶密碼強度比 Azure AD B2C 強制執行的[強式密碼強度](../active-directory/authentication/concept-sspr-policy.md)弱，您可以停用強式密碼需求。 如需詳細資訊，請參閱[密碼原則屬性](manage-user-accounts-graph-api.md#password-policy-property)。

## <a name="next-steps"></a>後續步驟

GitHub 上的[azure ad b2c/使用者-遷移](https://github.com/azure-ad-b2c/user-migration)存放庫包含順暢的遷移自訂原則範例和 REST API 的程式碼範例：

[順暢的使用者遷移自訂原則 & REST API 程式碼範例](https://aka.ms/b2c-account-seamless-migration)
