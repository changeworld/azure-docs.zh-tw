---
title: 如何管理 Azure AD 中的非活動使用者帳戶 |微軟文件
description: 瞭解如何偵測與處理 Azure AD 中已過時的使用者帳戶
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886036"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>如何:Azure AD 中管理非活動使用者帳戶

在大型環境中,當員工離開組織時,使用者帳戶並不總是被刪除。 作為 IT 管理員,您希望檢測和處理這些過時的使用者帳戶,因為它們存在安全風險。

本文介紹在 Azure AD 中處理過時的使用者帳戶的方法。 

## <a name="what-are-inactive-user-accounts"></a>什麼是非活動使用者帳戶?

非活動帳戶是組織成員不再需要的使用者帳戶來訪問您的資源。 非活動帳戶的一個關鍵標識符是,它們有*一段時間*沒有用於登錄到您的環境。 由於非活動帳戶綁定到登錄活動,因此您可以使用成功檢測它們的最後一個登錄的時間戳。 

此方法的挑戰是定義在環境的情況下 *,一段時間*意味著什麼。 例如,使用者可能*一段時間*沒有登錄到環境,因為他們正在度假。 在定義非活動使用者帳戶的增量時,您需要考慮不登錄到環境的所有合法原因。 在許多組織中,非活動使用者帳戶的增量在 90 到 180 天之間。 

上次成功登錄提供了使用者對資源訪問的持續需求的潛在見解。  它可以幫助確定是否仍然需要組成員身份或應用訪問許可權或是否可以刪除。 對於外部使用者管理,您可以瞭解外部使用者是否仍在租戶中處於活動狀態或應清理。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何偵測非活動使用者帳戶

通過評估**Microsoft 圖形**API 的**符號 InActivity**資源類型公開的**上次SignInDateTime**屬性,可以檢測非活動帳戶。 使用此屬性,您可以為以下方案實現解決方案:

- **依名稱搜尋使用者**:在此方案中,您可以依名稱搜尋特定使用者,這使您能夠評估最後一個 SignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **依日期進行使用者:** 在此機制中,您要求在指定日期之前使用最後一個 SignInDateTime 的使用者清單:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>您所需了解的事情

本節列出了您需要瞭解的最後一個SignInDateTime屬性的內容。

### <a name="how-can-i-access-this-property"></a>如何訪問此屬性?

**最後一個SignInDateTime**屬性由[微軟圖形REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)的[符號不活動資源類型](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta)公開。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>最後一個SignInDateTime屬性是否可以通過獲取 AzureAdUser cmdlet 提供?

否。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>存取屬性需要哪個版本的 Azure AD?

您可以在 Azure AD 的所有版本中存取此屬性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>讀取屬性需要獲得什麼許可權?

要讀取此屬性,您需要授予以下權限: 

- 稽核記錄紀錄.閱讀。所有
- 組織.閱讀。所有  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD 何時更新屬性?

成功的每個互動式登錄都會導致基礎數據存儲的更新。 通常,成功的登錄將在 10 分鐘內顯示在相關的登錄報告中。
 

### <a name="what-does-a-blank-property-value-mean"></a>空白屬性值是什麼意思?

要生成最後一個 SignInDateTime 時間戳,您需要成功登錄。 由於最後一個SignInDateTime屬性是一個新功能,因此,如果出現以下情況,則最後一個SignInDateTime屬性的值可以為空:

- 用戶的最後一次成功登錄是在此功能發佈之前(2019 年 12 月 1 日)。
- 受影響的使用者帳戶從未用於成功登錄。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
* [稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
