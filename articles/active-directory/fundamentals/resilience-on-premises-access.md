---
title: 使用應用程式 Proxy 來建立應用程式存取的復原能力
description: 使用應用程式 Proxy 以復原方式存取內部部署應用程式的架構設計人員和 IT 系統管理員指南
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
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919419"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>使用應用程式 Proxy 來建立應用程式存取的復原能力

應用程式 Proxy 是 Azure AD 的一項功能，可讓使用者從遠端用戶端存取內部部署 Web 應用程式。 應用程式 Proxy 同時包含雲端中的「應用程式 Proxy」服務，以及在內部部署伺服器上執行的「應用程式 Proxy」連接器。 

使用者透過透過應用程式 Proxy 發佈的 URL 來存取內部部署資源。 系統會將他們重新導向至 Azure AD 登入頁面。 Azure AD 中的應用程式 Proxy 服務會將權杖傳送至公司網路中的「應用程式 Proxy」連接器，此連接器會將權杖傳遞給內部部署 Active Directory 驗證的使用者接著可以存取內部部署資源。 在下圖 [中，連接器](../manage-apps/application-proxy-connectors.md) 會顯示在 [連接器群組](../manage-apps/application-proxy-connector-groups.md)中。

> [!IMPORTANT]
> 當您透過應用程式 Proxy 發佈應用程式時，您必須 [針對應用程式 proxy 連接器，執行容量規劃和適當的複製](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)。

![應用程式 y 的架構圖](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>如何? 執行應用程式 Proxy？

若要使用 Azure AD 應用程式 Proxy 來執行遠端存取，請參閱下列資源。

* [規劃應用程式 Proxy 部署](../manage-apps/application-proxy-deployment-plan.md)

* [高可用性和負載平衡的最佳作法](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [設定 proxy 伺服器](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [設計彈性的存取控制策略](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)
