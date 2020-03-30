---
title: Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告 | Microsoft Docs
description: 了解 Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014458"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Azure 入口網站中標幟為有風險的使用者報告

Azure Active Directory (Azure AD) 會偵測與使用者帳戶相關的可疑動作。 對於每個檢測到的操作，都會創建一個稱為[風險檢測](concept-risk-events.md)的記錄。

您可以從 [Azure 入口網站](https://portal.azure.com)中，藉由選取 [Azure Active Directory]**** 刀鋒視窗，然後瀏覽至 [安全性]**** 區段，來存取安全性報告。 

檢測到的風險檢測用於計算：

- **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 

- **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 

要瞭解如何配置觸發這些風險檢測的策略，請參閱[如何配置使用者風險策略](../identity-protection/howto-user-risk-policy.md)。 

![有風險的登入](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>您需要哪項 Azure AD 授權才能存取有風險的使用者報告？  

所有 Azure Active Directory 版本都會為您提供標幟為有風險的使用者報告。 不過，報告細微性層級因版本而異： 

- 在 [Azure Active Directory Free 和 Basic 版本]**** 中，您會取得標幟為有風險的使用者清單。 

- 此外 **，Azure 活動目錄高級 1**版允許您檢查已檢測到每個報表的某些基本風險檢測。 

- **Azure Active Directory 高級版**為您提供了有關所有潛在風險檢測的最詳細資訊，還使您能夠配置自動回應配置的風險級別的安全性原則。


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Azure AD 免費和基本版本的有風險使用者報告

Azure AD 免費和基本版本中標幟為有風險的使用者報告，會提供可能遭到盜用的使用者帳戶清單。 

![有風險的登入](./media/concept-user-at-risk/03.png)

選取使用者時會提供登入資訊。 針對有風險的使用者，您可以檢閱使用者的登入記錄，如有必要，請重設密碼。

此對話方塊會提供選項以便：

- 下載報告
- 搜尋使用者

    ![有風險的登入](./media/concept-user-at-risk/16.png)

如需詳細資訊，您必須要有進階授權。

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Azure AD 進階版本的有風險使用者報告

Azure AD 進階版本中標幟為有風險的使用者報告可提供：

- 可能已遭盜用的使用者帳戶清單 

- 有關已檢測到[的風險檢測類型的](concept-risk-events.md)聚合資訊

- 下載報告的選項

- 選擇設定[使用者風險補救原則](../identity-protection/howto-user-risk-policy.md)  

![有風險的登入](./media/concept-user-at-risk/71.png)

當您選取使用者時，即會取得這位使用者的詳細報告檢視，讓您能夠：

- 開啟 [所有登入] 檢視

- 重設使用者的密碼

- 關閉所有事件

- 調查使用者報告的風險檢測。 

![有風險的登入](./media/concept-user-at-risk/324.png)

要調查風險檢測，請從清單中選擇一個來打開此風險檢測**的"詳細資訊"** 邊欄選項卡。 在 **"詳細資訊"** 邊欄選項卡上，您可以選擇手動關閉風險檢測或重新啟動手動關閉的風險檢測。 

![有風險的登入](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>後續步驟

- [如何配置使用者風險策略](../identity-protection/howto-user-risk-policy.md)
- [如何設定風險補救原則](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

