---
title: 如何管理 Azure AD 中的非使用中使用者帳戶 | Microsoft Docs
description: 了解如何在 Azure AD 中偵測並處理已淘汰的使用者帳戶
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92f6f32298dcccca4eba08fd25de0504416e5560
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608138"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>作法：管理 Azure AD 中的非使用中使用者帳戶

在大型環境中，當員工離開組織時，其使用者帳戶不一定會遭到刪除。 身為 IT 系統管理員，您會想要偵測並處理這些已淘汰的使用者帳戶，因為這些帳戶代表著安全性風險。

本文說明如何在 Azure AD 中處理已淘汰使用者帳戶的方法。 

## <a name="what-are-inactive-user-accounts"></a>什麼是非使用中的使用者帳戶？

非使用中帳戶是組織成員不再需要用來取得您資源存取權的使用者帳戶。 要識別非使用中帳戶的一個重點，就是這些帳戶「已有一段時間未用來登入您的環境」。 由於非使用中帳戶會與登入活動繫結，因此您可以使用上次成功登入的時間戳記來偵測。 

這種方法的挑戰性在於，定義您環境中「一段時間」的所代表的時間長短。 例如，使用者可能會有「一段時間」不會登入環境 ，因為他們在休假。 定義非使用中使用者帳戶的差異時，您必須考慮不登入環境的所有合理原因。 在許多組織中，非使用中使用者帳戶的差異介於 90 到 180 天之間。 

上次的成功登入可讓您深入了解使用者是否需要持續存取資源。  此資訊可協助您判斷是否仍需要該人員是否需要群組成員資格或應用程式存取權，或是否可移除。 對於外部使用者管理，您可以了解外部使用者是否仍活躍在租用戶中，或應加以清除。 

    
## <a name="how-to-detect-inactive-user-accounts"></a>如何偵測非使用中的使用者帳戶

您可以評估 **Microsoft Graph** API 的 **signInActivity** 資源類型所公開的 **lastSignInDateTime** 屬性，來偵測非使用中的帳戶。 使用此屬性，您可以針對下列案例實作解決方案：

- **依名稱尋找使用者**：在此案例中，您會依名稱搜尋特定使用者，這可讓您評估 lastSignInDateTime：`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **依日期尋找使用者**：在此案例中，您會要求 lastSignInDateTime 尋找指定日期之前的使用者清單：`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>您所需了解的事情

本節列出您需要知道的 lastSignInDateTime 屬性資訊。

### <a name="how-can-i-access-this-property"></a>如何存取這個屬性？

**lastSignInDateTime** 屬性是由 [Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph) 的 [signInActivity 資源類型](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta)所公開。   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>lastSignInDateTime 屬性是否可透過 Get-AzureAdUser Cmdlet 取得？

否。

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>我需要何種版本的 Azure AD 來存取屬性？

您可以在所有版本的 Azure AD 中存取此屬性。

### <a name="what-permission-do-i-need-to-read-the-property"></a>我需要何種權限才能讀取屬性？

若要讀取這個屬性，您必須授與下列權限： 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Azure AD 會在何時更新屬性？

每個成功的互動式登入都會更新基礎資料存放區。 通常，成功的登入會在 10 分鐘內顯示在相關的登入報告中。
 

### <a name="what-does-a-blank-property-value-mean"></a>空白屬性值有何意義？

若要產生 lastSignInDateTime 時間戳記，您必須成功登入。 由於 lastSignInDateTime 屬性是一項新功能，因此如果發生下列情況，lastSignInDateTime 屬性的值可能為空白：

- 使用者的上次成功登入發生在此功能發行之前 (2019 年 12 月 1 日)。
- 受影響的使用者帳戶從未成功登入過。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
* [稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
