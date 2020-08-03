---
title: Azure 資料共用的嚴重損壞修復
description: Azure 資料共用的嚴重損壞修復
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 61804aaf65a97485e2b2b5bb4869c335a14ce812
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513553"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 資料共用的嚴重損壞修復

本文說明如何設定 Azure 資料共用的嚴重損壞修復環境。 Azure 資料中心中斷很罕見，但可能會從數分鐘到數小時。 資料中心中斷可能會對依賴資料提供者所共用之資料的環境造成中斷。 依照這篇文章中所述的步驟進行，資料提供者可以在裝載資料共用的主要區域發生資料中心中斷的情況下，繼續與資料取用者共用資料。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>達到 Azure 資料共用的商務持續性

若要針對資料中心中斷進行準備，資料提供者可以在次要區域中布建資料共用環境。 可以採取量值，以確保發生資料中心中斷時的順暢容錯移轉。 

資料提供者可在其他區域中布建次要 Azure 資料共用資源。 這些資料共用資源可以設定為包含存在於主要 Azure 資料共用資源中的共用和資料集。 在設定 DR 環境或稍後設定時，他們可以邀請資料取用者到次要共用（亦即 做為手動容錯移轉步驟的一部分）。

如果資料取用者在布建用於 DR 的次要環境中有作用中的共用訂用帳戶，則可以啟用快照集排程作為容錯移轉的一部分。 如果資料取用者不想訂閱次要區域以進行 DR，可以稍後再邀請到次要共用。 

資料取用者可以有作用中的共用訂用帳戶，以供 DR 之用，或者資料提供者可以稍後在手動容錯移轉程式中邀請它們。 

## <a name="related-information"></a>相關資訊

- [商務持續性和嚴重損壞修復](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [在您的 BCDR 策略中加入高可用性](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。




