---
title: 使用 Azure Active Directory 中的持續存取評估來建立復原能力
description: 使用 CAE 的架構設計人員和 IT 系統管理員指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a12cc3299fbb5916afba48d2608024f28999634
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724622"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>使用連續存取評估來建立復原能力

[持續存取評估](../conditional-access/concept-continuous-access-evaluation.md) (CAE) 可讓 Azure AD 應用程式訂閱可進行評估和強制執行的重大事件。 這包括評估下列事件：

* 正在刪除或停用的使用者帳戶

* 使用者的密碼已變更

* 已為使用者啟用 MFA。

* 系統管理員明確撤銷權杖。

* 偵測到提升的使用者風險。

因此，應用程式可以根據 Azure AD 所發出的事件來拒絕未到期的權杖，如下圖所示。

![CAE 的 conceptualiagram](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE 有何説明？

這種機制可讓 Azure AD 發出較長的權杖，同時讓應用程式能夠撤銷存取權，並只在需要時才強制重新驗證。 此模式的淨結果較少取得權杖的呼叫，這表示端對端流程的復原能力更高。 

若要使用 CAE，服務和用戶端都必須具備 CAE 功能。 Microsoft 365 的服務，例如 Exchange Online、小組和 SharePoint Online 支援 CAE。 在用戶端上，使用這些 Office 365 服務的瀏覽器型體驗 (例如 Outlook Web App) 和特定版本的 Office 365 原生用戶端都具有 CAE 功能。 更多 Microsoft 雲端服務將變成 CAE 功能。

Microsoft 與業界合作，以建立可讓協力廠商應用程式使用這項功能的 [標準](https://openid.net/wg/sse/) 。 您也可以開發具備 CAE 功能的應用程式。 如需詳細資訊，請參閱如何在您的應用程式中建立恢復功能。

## <a name="how-do-i-implement-cae"></a>如何? 執行 CAE？

* 在 Azure AD 安全性設定中[啟用 CAE](../conditional-access/concept-continuous-access-evaluation.md) 。

* 確定您的組織使用 Microsoft Office 原生應用程式的 [相容版本](../conditional-access/concept-continuous-access-evaluation.md) 。

* [優化您的重新驗證提示](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

 
## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)