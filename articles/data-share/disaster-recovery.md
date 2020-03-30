---
title: Azure 資料共用的災害復原
description: Azure 資料共用的災害復原
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483176"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 資料共用的災害復原

在本文中，我們將介紹如何為 Azure 資料共用配置災害復原環境。 Azure 資料中心中斷很少發生，但可以持續幾分鐘到幾小時。 資料中心中斷可能導致依賴于資料供應商共用的資料的環境中斷。 通過按照本文中詳述的步驟，資料提供程式可以繼續與其資料消費者共用資料，在託管資料共用的主區域的資料中心中斷時。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>實現 Azure 資料共用的業務連續性

為了準備資料中心中斷，資料提供程式可以在次要區域中預配資料共用環境。 可以採取一些措施，確保在發生資料中心中斷時順利容錯移轉。 

資料提供程式可以在其他區域中預配輔助 Azure 資料共用資源。 可以配置這些資料共用資源，以包括主資料共用環境中存在的資料集。 在配置 DR 環境時，資料消費者可以添加到資料共用中，或者在以後的時間點（即 作為手動容錯移轉步驟的一部分）。

如果資料消費者在為 DR 目的預配的輔助環境中具有活動共用訂閱，則可以在容錯移轉時啟用快照計畫。 如果資料消費者不想為 DR 目的訂閱次要區域，則可以在稍後時間點邀請他們加入輔助資料共用。 

資料消費者可以具有為 DR 目的處於空閒狀態的活動共用訂閱，或者資料提供程式可以在以後的時間點將其添加為手動容錯移轉過程的一部分。 

## <a name="related-information"></a>相關資訊

- [業務連續性和災害復原](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [在您的 BCDR 策略中加入高可用性](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。




