---
title: 使用者遷移方法
titleSuffix: Azure AD B2C
description: 通過使用大量匯入或無縫遷移方法將使用者帳戶從其他標識提供程式遷移到 Azure AD B2C。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332338"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>將使用者遷移到 Azure AD B2C

從另一個標識提供程式遷移到 Azure 活動目錄 B2C（Azure AD B2C）可能還需要遷移現有使用者帳戶。 這裡討論了兩種遷移方法，*即大量匯入*和*無縫遷移*。 使用這兩種方法，都需要編寫一個應用程式或腳本，該應用程式或腳本使用[Microsoft 圖形 API](manage-user-accounts-graph-api.md)在 Azure AD B2C 中創建使用者帳戶。

## <a name="bulk-import"></a>大量匯入

在大量匯入流中，遷移應用程式對每個使用者帳戶執行以下步驟：

1. 從舊標識提供程式讀取使用者帳戶，包括其當前憑據（使用者名和密碼）。
1. 使用當前憑據在 Azure AD B2C 目錄中創建相應的帳戶。

在以下兩種情況下使用大量匯入流：

- 您可以訪問使用者的純文字憑據（其使用者名和密碼）。
- 憑據已加密，但您可以解密它們。

有關以程式設計方式創建使用者帳戶的資訊，請參閱[使用 Microsoft 圖形管理 Azure AD B2C 使用者帳戶](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>無縫遷移

如果無法訪問舊標識提供程式中的純文字密碼，請使用無縫遷移流。 例如，在：

- 密碼以單向加密格式存儲，例如使用雜湊函數。
- 密碼由舊版標識提供程式以您無法訪問的方式存儲。 例如，當標識提供程式通過調用 Web 服務來驗證憑據時。

無縫遷移流仍然需要使用者帳戶的批量遷移，但隨後使用[自訂策略](custom-policy-get-started.md)查詢[REST API（](custom-policy-rest-api-intro.md)您創建的）來在首次登錄時設置每個使用者的密碼。

因此，無縫遷移流有兩個階段：*大量匯入*和*設置憑據*。

### <a name="phase-1-bulk-import"></a>第 1 階段：批量進口

1. 您的遷移應用程式從舊的標識提供程式讀取使用者帳戶。
1. 遷移應用程式在 Azure AD B2C 目錄中創建相應的使用者帳戶，但不會*設置密碼*。

### <a name="phase-2-set-credentials"></a>第 2 階段：設置憑據

完成帳戶批量遷移後，自訂策略和 REST API 然後在使用者登錄時執行以下操作：

1. 閱讀與輸入的電子郵件地址對應的 Azure AD B2C 使用者帳戶。
1. 通過評估布林擴充屬性，檢查帳戶是否已標記為遷移。
    - 如果擴充屬性返回`True`，請調用 REST API 以根據舊標識提供程式驗證密碼。
      - 如果 REST API 確定密碼不正確，則向使用者返回友好錯誤。
      - 如果 REST API 確定密碼正確，請將密碼寫入 Azure AD B2C 帳戶，並將布林擴充屬性`False`更改為 。
    - 如果布林擴充屬性返回`False`，則像往常一樣繼續登錄過程。

要查看自訂策略和 REST API 的示例，請參閱 GitHub 上的[無縫使用者遷移示例](https://aka.ms/b2c-account-seamless-migration)。

![無縫遷移方法的流程圖圖，用於使用者遷移](./media/user-migration/diagram-01-seamless-migration.png)<br />*圖表：無縫遷移流*

## <a name="best-practices"></a>最佳作法

### <a name="security"></a>安全性

無縫遷移方法使用您自己的自訂 REST API 來驗證使用者針對舊標識提供程式的憑據。

**您必須保護您的 REST API 免受暴力攻擊。** 攻擊者可以提交多個密碼，希望最終猜測使用者的憑據。 為了説明擊敗此類攻擊，當登錄嘗試數超過特定閾值時，停止向 REST API 提供請求。 此外，保護 Azure AD B2C 和 REST API 之間的通信。 要瞭解如何保護用於生產的 RESTful API，請參閱安全[RESTful API](secure-rest-api.md)。

### <a name="user-attributes"></a>使用者屬性

並非所有舊標識提供程式中的資訊都應遷移到 Azure AD B2C 目錄。 在遷移之前，確定要存儲在 Azure AD B2C 中的相應使用者屬性集。

- **在**Azure AD B2C 中存儲
  - 使用者名、密碼、電子郵件地址、電話號碼、會員編號/識別碼。
  - 隱私政策和使用者授權合約的同意標記。
- **不要**存儲在 Azure AD B2C 中
  - 敏感性資料，如信用卡號、社會保險號 （SSN）、醫療記錄或政府或行業合規機構監管的其他資料。
  - 行銷或溝通偏好、使用者行為和見解。

### <a name="directory-clean-up"></a>目錄清理

在開始遷移過程之前，請借此機會清理目錄。

- 標識要存儲在 Azure AD B2C 中的使用者屬性集，並僅遷移所需的屬性。 如有必要，您可以創建[自訂屬性](custom-policy-custom-attributes.md)來存儲有關使用者的更多資料。
- 如果要從具有多個身份驗證源的環境（例如，每個應用程式都有自己的使用者目錄）遷移到 Azure AD B2C 中的統一帳戶。
- 如果多個應用程式具有不同的使用者名，則可以使用標識集合將所有應用程式存儲在 Azure AD B2C 使用者帳戶中。 關於密碼，讓使用者選擇一個並在目錄中設置它。 例如，通過無縫遷移，應僅將所選密碼存儲在 Azure AD B2C 帳戶中。
- 在遷移之前刪除未使用的使用者帳戶，或者不遷移陳舊的帳戶。

### <a name="password-policy"></a>密碼原則

如果要遷移的帳戶的密碼強度低於 Azure AD B2C 強制[的強式密碼強度](../active-directory/authentication/concept-sspr-policy.md)，則可以禁用強式密碼要求。 有關詳細資訊，請參閱[密碼原則屬性](manage-user-accounts-graph-api.md#password-policy-property)。

## <a name="next-steps"></a>後續步驟

GitHub 上的[azure ad-b2c/使用者遷移](https://github.com/azure-ad-b2c/user-migration)存儲庫包含無縫遷移自訂策略示例和 REST API 代碼示例：

[無縫使用者遷移自訂策略& REST API 代碼示例](https://aka.ms/b2c-account-seamless-migration)
