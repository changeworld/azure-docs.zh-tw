---
title: Azure AD Connect 雲端同步支援的拓撲和案例
description: 深入瞭解各種內部部署和 Azure Active Directory (Azure AD) 使用 Azure AD Connect 雲端同步處理的拓撲。
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
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613097"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect 雲端同步支援的拓撲和案例
本文說明各種內部部署和 Azure Active Directory (Azure AD) 使用 Azure AD Connect 雲端同步處理的拓撲。本文僅包含支援的設定和案例。

> [!IMPORTANT]
> Microsoft 不支援在正式記載的設定或動作之外，修改或操作 Azure AD Connect 雲端同步處理。 任何這些設定或動作可能會導致 Azure AD Connect 雲端同步的狀態不一致或不受支援。因此，Microsoft 無法針對這類部署提供技術支援。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>所有案例和拓撲的注意事項
以下是選取解決方案時要牢記在心的資訊清單。

- 必須在所有樹系中唯一識別使用者和群組
- 雲端同步處理不會跨樹系進行比對
- 使用者或群組必須只在所有樹系中表示一次
- 系統會自動選擇物件的來源錨點。  它會使用 ConsistencyGuid （如果有的話），否則會使用 ObjectGUID。
- 您無法變更用於來源錨點的屬性。

## <a name="single-forest-single-azure-ad-tenant"></a>單一樹系、單一 Azure AD 租用戶
![此圖顯示單一樹系和單一租使用者的拓撲。](media/tutorial-single-forest/diagram-2.png)

最簡單的拓撲是單一內部部署樹系、一或多個網域，以及單一 Azure AD 租使用者。  如需此案例的範例，請參閱[教學課程：具有單一 Azure AD 租使用者的單一樹](tutorial-single-forest.md)系


## <a name="multi-forest-single-azure-ad-tenant"></a>多樹系、單一 Azure AD 租使用者
![多樹系和單一租使用者的拓撲](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

常見拓撲是多個 AD 樹系、一或多個網域，以及單一 Azure AD 租使用者。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD Connect 的現有樹系、具有雲端布建的新樹系
![此圖顯示現有樹系和新樹系的拓撲。](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

此案例是拓撲類似于多樹系案例，不過這項作業牽涉到現有的 Azure AD Connect 環境，然後使用 Azure AD Connect 雲端同步處理來帶入新的樹系。 如需此案例的範例，請參閱[教學課程：具有單一 Azure AD 租使用者的現有樹](tutorial-existing-forest.md)系

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>在現有的混合式 AD 樹系中試驗 Azure AD Connect cloud sync
![單一樹系和單一租使用者的拓撲： ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) 試驗案例牽涉到相同樹系中的 Azure AD Connect 和 Azure AD Connect 雲端同步處理，並據以設定使用者和群組的範圍。 注意：物件應該只在其中一個工具的範圍內。 

如需此案例的範例，請參閱 [教學課程：在現有的同步 AD 樹系中試驗 Azure AD Connect cloud sync](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)

