---
title: 保護 Azure Active Directory 中資源的外部存取
description: 保護內部資源外部存取的架構設計人員和 IT 系統管理員指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39300c887245a99dce2913661f6492c85a1e6722
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743897"
---
# <a name="securing-external-access-to-resources"></a>保護資源的外部存取

與外部夥伴進行安全共同作業，可確保適當的外部夥伴可適當地存取內部資源。 透過整體治理方法，您可以降低安全性風險、符合合規性目標，並確保您知道誰有權存取。

Ungoverned 共同作業會導致存取權的擁有權不清楚，以及公開敏感資源的可能性。 移至安全且控管的共同作業，可確保外部使用者的存取權具有明確的擁有權和責任。 這包括：

* 管理可存取資源的外部組織和他們內部的使用者。

* 確保在適當的情況下，能夠適當地進行存取、檢查和時間限制。

* 讓企業擁有者能夠管理 IT 建立的防護滑軌中的共同作業。

如果您必須符合合規性架構，受管制的共同作業可讓您證明存取」適當性。

Microsoft 提供完整的工具套件，以安全地進行外部存取。  Azure Active Directory (Azure AD) B2B 共同作業是在任何外部共同作業計畫的中心。 Azure AD B2B 可以與 Azure AD 中的其他工具以及 Microsoft 365 services 中的工具整合，以協助保護及管理您的外部存取。

本檔集的設計目的是要讓您從臨機操作或鬆散管理的外部共同作業移至更安全的狀態。 

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。


1. [判斷您所需的外部存取安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
