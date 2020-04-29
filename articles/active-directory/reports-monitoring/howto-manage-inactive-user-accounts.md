---
title: 如何管理 Azure AD 中的非使用中使用者帳戶 |Microsoft Docs
description: 瞭解如何在 Azure AD 中偵測並處理已過時的使用者帳戶
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886036"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>如何：管理 Azure AD 中的非使用中使用者帳戶

在大型環境中，當員工離開組織時，不一定會刪除使用者帳戶。 身為 IT 系統管理員，您會想要偵測並處理這些過時的使用者帳戶，因為它們代表安全性風險。

本文說明在 Azure AD 中處理過時使用者帳戶的方法。 

## <a name="what-are-inactive-user-accounts"></a>什麼是非使用中的使用者帳戶？

非使用中帳戶是組織成員不再需要的使用者帳戶，以取得您的資源存取權。 非使用中帳戶的一個金鑰識別碼，就是它們尚未用來登入*您的環境*。 由於非使用中帳戶會系結至登入活動，因此您可以使用上次成功偵測的時間戳記。 

這個方法的挑戰是要定義，在您的環境中的情況下，會*有何意義*。 例如，使用者在*一段*時間內可能不會登入環境，因為他們在休假。 定義非作用中使用者帳戶的差異時，您必須考慮不登入環境的所有合理原因。 在許多組織中，非使用中使用者帳戶的差異介於90到180天之間。 

上次成功登入可讓您深入瞭解使用者對於資源的存取權。  它可協助您判斷是否仍需要群組成員資格或應用程式存取權，或是否可將其移除。 針對外部使用者管理，您可以瞭解外部使用者是否仍在租使用者中，或應加以清除。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何偵測非使用中的使用者帳戶

您可以藉由評估**Microsoft Graph** API 的**signInActivity**資源類型所公開的**lastSignInDateTime**屬性，來偵測非使用中的帳戶。 使用此屬性，您可以針對下列案例執行解決方案：

- **依名稱的使用者**：在此案例中，您會依名稱搜尋特定使用者，這可讓您評估 lastSignInDate：`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **依日期的使用者**：在此案例中，您會要求在指定的日期之前具有 lastSignInDateTime 的使用者清單：`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>您必須知道的事項

本節列出您需要知道的 lastSignInDateTime 屬性。

### <a name="how-can-i-access-this-property"></a>如何存取這個屬性？

**LastSignInDateTime**屬性是由[Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph)的[signInActivity 資源類型](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta)所公開。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>LastSignInDateTime 屬性是否可透過 New-azureaduser Cmdlet 取得？

否。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>我需要何種版本的 Azure AD 來存取屬性？

您可以在所有版本的 Azure AD 中存取此屬性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>我需要什麼許可權才能讀取屬性？

若要讀取這個屬性，您必須授與下列許可權： 

- AuditLogs. 讀取. 全部
- 組織。全部  


### <a name="when-does-azure-ad-update-the-property"></a>何時 Azure AD 更新屬性？

每個成功的互動式登入都會產生基礎資料存放區的更新。 通常，成功的登入會在10分鐘內顯示于相關的登入報告中。
 

### <a name="what-does-a-blank-property-value-mean"></a>空白屬性值的意義為何？

若要產生 lastSignInDateTime 時間戳記，您必須成功登入。 由於 lastSignInDateTime 屬性是一項新功能，因此，如果發生下列情況，lastSignInDateTime 屬性的值可能會空白：

- 在此功能發行之前，使用者的上次成功登入（2019年12月1日）。
- 受影響的使用者帳戶從未用於成功登入。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
* [稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
