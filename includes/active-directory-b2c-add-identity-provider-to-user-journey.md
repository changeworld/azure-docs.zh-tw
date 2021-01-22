---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674217"
---
## <a name="add-a-user-journey"></a>新增使用者旅程圖 

此時，已設定身分識別提供者，但尚未在任何登入頁面中提供。 如果您沒有自己的自訂使用者旅程圖，請建立現有範本使用者旅程圖的複本，否則請繼續進行下一個步驟。 

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `Id="CustomSignUpSignIn"` 。

## <a name="add-the-identity-provider-to-a-user-journey"></a>將身分識別提供者新增至使用者旅程圖 

現在您已有使用者旅程圖，請將新的身分識別提供者新增至使用者旅程圖。 您會先新增登入按鈕，然後將按鈕連結至動作。 動作是您稍早建立的技術設定檔。

1. 尋找包含 `Type="CombinedSignInAndSignUp"` 或使用者旅程圖中的協調流程步驟元素 `Type="ClaimsProviderSelection"` 。 這通常是第一個協調流程步驟。 **>claimsproviderselections >claimsproviderselection** 元素包含使用者可登入的身分識別提供者清單。 元素的順序會控制向使用者顯示的登入按鈕順序。 加入 **ClaimsProviderSelection** XML 元素。 將 **>targetclaimsexchangeid** 的值設定為易記名稱。

1. 在下一個協調流程步驟中，加入 **ClaimsExchange** 元素。 將 **識別碼** 設定為目標宣告交換識別碼的值。將 **>technicalprofilereferenceid** 的值更新為您稍早建立之技術設定檔的識別碼。

下列 XML 示範使用者旅程圖的前兩個協調流程步驟與身分識別提供者：