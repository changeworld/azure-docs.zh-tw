---
title: Azure AD 連接雲配置支援的拓撲和方案
description: 本主題介紹先決條件和硬體要求雲配置。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620879"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD 連接雲配置支援的拓撲和方案
本文介紹了使用 Azure AD 連接雲預配的各種本地和 Azure 活動目錄 （Azure AD） 拓撲。 本文僅包括受支援的配置和方案。

> [!IMPORTANT]
> Microsoft 不支援在正式記錄的配置或操作之外修改或操作 Azure AD Connect 雲資源調配。 這些配置或操作中的任何一種都可能導致 Azure AD Connect 雲配置的不一致或不受支援的狀態。 如此一來，Microsoft 無法提供這類部署的技術支援人員。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>關於所有方案和拓撲要記住的事項
以下是選擇解決方案時要記住的資訊清單。

- 必須在所有林中唯一標識使用者和組
- 雲預配不會跨林匹配
- 使用者或組必須在所有林中僅表示一次
- 物件的源錨點將自動選擇。  它使用 ms-DS-一致性 Guid（如果存在），否則將使用 ObjectGUID。
- 不能更改用於源錨點的屬性。

## <a name="single-forest-single-azure-ad-tenant"></a>單一樹系、單一 Azure AD 租用戶
![單一樹系和單一租用戶的拓撲](media/plan-cloud-provisioning-topologies/single-forest.png)

最簡單的拓撲是單個本地林，具有一個或多個域，以及單個 Azure AD 租戶。  有關此方案的示例，請參閱[教程：具有單個 Azure AD 租戶的單個林](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>多林、單個 Azure AD 租戶
![多林和單個租戶的拓撲](media/plan-cloud-provisioning-topologies/multi-forest.png)

公共拓撲是多個 AD 林，具有一個或多個域，以及單個 Azure AD 租戶。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD 連接的現有林，具有雲預配的新林
![單一樹系和單一租用戶的拓撲](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

此方案是拓撲類似于多林方案，但是此方案涉及現有的 Azure AD 連接環境，然後使用 Azure AD Connect 雲預配引入新林。  有關此方案的示例，請參閱[教程：具有單個 Azure AD 租戶的現有林](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>在現有混合 AD 林中試用 Azure AD 連接雲資源調配
![單個林和單個租戶](media/plan-cloud-provisioning-topologies/migrate.png)的拓撲 試驗方案涉及在同一林中同時存在 Azure AD 連接和 Azure AD 連接雲資源，並相應地對使用者和組進行範圍界定。 注： 物件應僅位於其中一個工具的作用域中。 

有關此方案的示例，請參閱[教程：在現有同步 AD 林中試用 Azure AD 連接雲資源](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

