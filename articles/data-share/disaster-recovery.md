---
title: Azure 資料共用的嚴重損壞修復
description: Azure 資料共用的嚴重損壞修復
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483176"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 資料共用的嚴重損壞修復

在本文中，我們將逐步解說如何設定 Azure 資料共用的嚴重損壞修復環境。 Azure 資料中心中斷很罕見，但可能會從數分鐘到數小時。 資料中心中斷可能會對依賴資料提供者所共用之資料的環境造成中斷。 依照這篇文章中所述的步驟進行，資料提供者可以在裝載資料共用的主要區域發生資料中心中斷的情況下，繼續與資料取用者共用資料。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>達到 Azure 資料共用的商務持續性

若要針對資料中心中斷進行準備，資料提供者可以在次要區域中布建資料共用環境。 您可以採取一些措施，以確保發生資料中心中斷的情況下，會順利進行容錯移轉。 

資料提供者可在其他區域中布建次要 Azure 資料共用資源。 這些資料共用資源可以設定為包含存在於主要資料共用環境中的資料集。 設定 DR 環境時，可以將資料取用者新增至資料共用，或在稍後的時間點新增（亦即 做為手動容錯移轉步驟的一部分）。

如果資料取用者在為 DR 目的而布建的次要環境中有作用中的共用訂用帳戶，則可以在容錯移轉過程中啟用快照集排程。 如果資料取用者不想訂閱次要區域以進行 DR，可以在稍後的時間點邀請他們進入次要資料共用。 

資料取用者可以有作用中的共用訂用帳戶，以供 DR 之用，或者資料提供者可以在稍後的時間點將它們新增為手動容錯移轉程式的一部分。 

## <a name="related-information"></a>相關資訊

- [商務持續性和嚴重損壞修復](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [在您的 BCDR 策略中加入高可用性](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。




