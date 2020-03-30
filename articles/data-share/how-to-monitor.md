---
title: 如何監視 Azure 資料共用
description: 瞭解如何監視 Azure 資料共用中的邀請狀態、共用訂閱和快照歷史記錄
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490519"
---
# <a name="monitor-azure-data-share"></a>監視 Azure Data Share  

本文介紹如何使用 Azure 資料共用監視資料共用。 作為資料供應商，您可以監視資料共用關係的各個方面。 資料消費者是否接受資料共用邀請，以及他們是否創建了共用訂閱並開始使用您的資料等詳細資訊均可進行監控。 

作為資料消費者，可以監視已觸發到 Azure 訂閱中的快照。 

## <a name="monitor-invitation-status"></a>監視邀請狀態

通過導航到已發送共用 -> 邀請，查看資料共用邀請的狀態。 

![邀請狀態](./media/invitation-status.png "邀請狀態") 

您的邀請可以處於三種狀態：

* 掛起 - 資料共用收件者尚未接受邀請。
* 已接受 - 資料共用收件者已接受邀請。
* 已拒絕 - 資料共用收件者已拒絕邀請。

> [!IMPORTANT]
> 如果在邀請已被接受後刪除邀請，則不等於吊銷存取權限。 如果要阻止將將來的快照複製到資料消費者存儲帳戶，則必須通過 *"共用訂閱"* 選項卡撤銷存取權限。 

## <a name="monitor-share-subscriptions"></a>監視共用訂閱

通過導航到已發送共用 ->共用訂閱，查看共用訂閱的狀態。 這將提供有關資料消費者在接受邀請後創建的活動訂閱的詳細資訊。 您可以通過選擇共用訂閱並選擇 *"撤銷"* 來阻止對資料消費者的未來更新。 

## <a name="snapshot-history"></a>快照歷史記錄 

在歷史記錄選項卡中，您可以查看已複製到資料消費者租戶的快照。 您可以監視每個快照間隔的頻率和持續時間。 

![快照歷史記錄](./media/sent-shares.png "快照歷史記錄") 

您可以通過按一下運行開始日期查看有關每個快照運行的更多詳細資訊。 

預設情況下，最多顯示 30 天的快照歷史記錄。 如果您需要查看超過 30 天的歷史，請導航到"監視 ->診斷設置"，然後選擇"**添加診斷設置**"。 您需要選擇存儲帳戶才能將這些日誌存儲到其中。 

![快照歷史記錄](./media/diagnostic-settings.png "診斷設定") 

## <a name="next-steps"></a>後續步驟 

瞭解有關[Azure 資料共用術語的更多](terminology.md)