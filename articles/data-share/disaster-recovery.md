---
title: Azure Data Share 的嚴重損壞修復
description: Azure Data Share 的嚴重損壞修復
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218692"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure Data Share 的嚴重損壞修復

本文說明如何設定 Azure Data Share 的嚴重損壞修復環境。 Azure 資料中心的中斷很罕見，但從數分鐘到數小時都可以持續進行。 資料中心中斷可能會讓依賴資料提供者所共用之資料的環境中斷。 依照本文中詳述的步驟，當裝載資料共用的主要區域發生資料中心中斷時，資料提供者可以繼續與資料取用者共用資料。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>實現 Azure Data Share 的商務持續性

若要為資料中心中斷做好準備，資料提供者可以在次要區域中布建資料共用環境。 您可以採取量值，以確保在發生資料中心中斷的情況下進行順暢的容錯移轉。 

資料提供者可在其他區域中布建次要 Azure Data Share 資源。 這些 Data Share 資源可以設定為包含存在於主要 Azure Data Share 資源中的共用和資料集。 他們可以在設定 DR 環境時，邀請資料取用者到次要共用，也可以在稍後進行 (例如 ) 手動容錯移轉步驟的一部分。

如果資料取用者在針對 DR 用途布建的次要環境中有作用中的共用訂用帳戶，則可以在容錯移轉時啟用快照集排程。 如果資料取用者不想要針對 DR 用途訂閱次要區域，則可以稍後將它們受邀加入次要共用。 

資料取用者可以有閒置的共用訂用帳戶，以供 DR 之用，或是資料提供者可以稍後在手動容錯移轉程式中邀請這些訂用帳戶。 

## <a name="related-information"></a>相關資訊

- [商務持續性和嚴重損壞修復](../best-practices-availability-paired-regions.md)
- [在您的 BCDR 策略中加入高可用性](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。