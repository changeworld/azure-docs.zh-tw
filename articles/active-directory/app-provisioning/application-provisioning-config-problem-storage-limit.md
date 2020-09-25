---
title: 儲存管理認證 Azure AD 資源庫應用程式的問題
description: 瞭解如何針對在設定 Azure Active Directory 資源庫應用程式的使用者布建時，儲存系統管理員認證時所面臨的問題進行疑難排解。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7a3340e72499087dce7773264272601dfce8a50f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266777"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>在設定 Azure Active Directory 資源庫應用程式之使用者佈建的情況下儲存系統管理員認證時發生問題 

在使用 Azure 入口網站針對企業應用程式設定[自動使用者佈建](user-provisioning.md)時，您可能會遇到以下情況：

* 針對應用程式所輸入的**系統管理員認證**有效，且 [測試連線]**** 按鈕可以運作。 不過，無法儲存認證，而且 Azure 入口網站傳回一般錯誤訊息。

如果同時為相同應用程式設定 SAML 型單一登入，錯誤最可能的原因是您已超過 Azure AD 內部針對憑證和認證所定義的每一應用程式儲存體限制。

Azure AD 目前針對所有憑證、祕密權杖、認證以及與單一應用程式執行個體關聯之相關設定資料 (在 Azure AD 中也稱為服務主體記錄) 的儲存體容量上限為 1024 個位元組。

在設定了 SAML 型單一登入時，用來簽署 SAML 權杖的憑證會儲存在這裡，且通常會耗用超過 50% 的空間。

使用者佈建設定期間所輸入的任何祕密權杖、URI、通知電子郵件地址、使用者名稱及密碼都會導致超過儲存空間限制。

## <a name="how-to-work-around-this-issue"></a>如何解決此問題 

目前有兩個可行的方法可以解決此問題：

1. **使用兩個資源庫應用程式執行個體，一個用於單一登入，一個用於使用者佈建** - 以資源庫應用程式 [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) 為例，您可以從資源庫新增 LinkedIn Elevate 並為其設定單一登入。 針對佈建，從 Azure AD 應用程式資源庫新增另一個 LinkedIn Elevate 執行個體，並將它命名為 "LinkedIn Elevate (Provisioning)"。 針對此第二個執行個體，設定[佈建](../saas-apps/linkedinelevate-provisioning-tutorial.md)而不是單一登入。 使用這個因應措施時，必須將相同的使用者和群組[指派](../manage-apps/assign-user-or-group-access-portal.md)給兩個應用程式。 

2. **減少所儲存的設定資料量** - 在 [佈建] 索引標籤之 [[系統管理員認證](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)] 區段中所輸入的所有資料，都會儲存在與 SAML 憑證相同的位置。 雖然可能無法減少整個資料的長度，但可移除某些選擇性設定欄位，例如 [通知電子郵件]****。

## <a name="next-steps"></a>後續步驟
[設定 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)
