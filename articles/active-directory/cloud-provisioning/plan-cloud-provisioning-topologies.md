---
title: Azure AD Connect 雲端布建支援的拓撲和案例
description: 深入瞭解各種內部部署和 Azure Active Directory (Azure AD) 使用 Azure AD Connect 雲端布建的拓撲。
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
ms.openlocfilehash: d442c980ad5bbe3b56eae127b6e9eaeddf380a4e
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526845"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect 雲端布建支援的拓撲和案例
本文說明各種內部部署和 Azure Active Directory (Azure AD 使用 Azure AD Connect 雲端布建的) 拓撲。 本文僅包含支援的設定和案例。

> [!IMPORTANT]
> Microsoft 不支援在正式記載的設定或動作之外修改或操作 Azure AD Connect 雲端布建。 任何這些設定或動作可能會導致 Azure AD Connect 雲端布建的狀態不一致或不受支援。 如此一來，Microsoft 無法提供這類部署的技術支援人員。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>所有案例和拓撲的注意事項
以下是選取解決方案時要牢記在心的資訊清單。

- 必須在所有樹系中唯一識別使用者和群組
- 雲端布建不會跨樹系進行比對
- 使用者或群組必須只在所有樹系中表示一次
- 系統會自動選擇物件的來源錨點。  它會使用 ConsistencyGuid （如果有的話），否則會使用 ObjectGUID。
- 您無法變更用於來源錨點的屬性。

## <a name="single-forest-single-azure-ad-tenant"></a>單一樹系、單一 Azure AD 租用戶
![單一樹系和單一租用戶的拓撲](media/plan-cloud-provisioning-topologies/single-forest.png)

最簡單的拓撲是單一內部部署樹系、一或多個網域，以及單一 Azure AD 租使用者。  如需此案例的範例，請參閱[教學課程：具有單一 Azure AD 租使用者的單一樹](tutorial-single-forest.md)系


## <a name="multi-forest-single-azure-ad-tenant"></a>多樹系、單一 Azure AD 租使用者
![多樹系和單一租使用者的拓撲](media/plan-cloud-provisioning-topologies/multi-forest.png)

常見拓撲是多個 AD 樹系、一或多個網域，以及單一 Azure AD 租使用者。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD Connect 的現有樹系、具有雲端布建的新樹系
![單一樹系和單一租用戶的拓撲](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

此案例是拓撲類似于多樹系案例，不過這項作業牽涉到現有的 Azure AD Connect 環境，然後使用 Azure AD Connect 雲端布建來建立新的樹系。  如需此案例的範例，請參閱[教學課程：具有單一 Azure AD 租使用者的現有樹](tutorial-existing-forest.md)系

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>在現有的混合式 AD 樹系中試驗 Azure AD Connect 雲端布建
![單一樹系和單一租使用者的拓撲， ](media/plan-cloud-provisioning-topologies/migrate.png) 試驗案例牽涉到相同樹系中的 Azure AD Connect 和 Azure AD Connect 雲端布建，並據以設定使用者和群組的範圍。 注意：物件應該只在其中一個工具的範圍內。 

如需此案例的範例，請參閱 [教學課程：在現有的同步 AD 樹系中試驗 Azure AD Connect 雲端布建](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

