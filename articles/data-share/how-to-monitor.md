---
title: 如何監視 Azure Data Share
description: 瞭解如何在 Azure Data Share 中監視邀請狀態、共用訂用帳戶和快照記錄
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910469"
---
# <a name="monitor-azure-data-share"></a>監視 Azure Data Share  

本文說明如何使用 Azure Data Share 來監視您的資料共用。 作為資料提供者，您可以監視資料共用關聯性的各個層面。 詳細資料，例如您的資料取用者是否已接受您對資料共用的邀請，以及他們是否已建立共用訂用帳戶並開始使用您的資料，都可供監視。 

作為資料取用者，您可以監視已觸發到您 Azure 訂用帳戶中的快照集。 

## <a name="monitor-invitation-status"></a>監視邀請狀態

流覽至 [已傳送的共用-> 邀請]，以查看資料共用邀請的狀態。 

![邀請狀態](./media/invitation-status.png "邀請狀態") 

您的邀請可以有三種狀態：

* 暫止-資料共用收件者尚未接受邀請。
* 已接受-資料共用收件者已接受邀請。
* 拒絕-資料共用收件者已拒絕邀請。

> [!IMPORTANT]
> 如果您在已接受邀請之後將其刪除，則不等於撤銷存取權。 如果您想要停止未來的快照集複製到您的資料取用者儲存體帳戶，您必須透過 [ *共用訂閱* ] 索引標籤撤銷存取權。 

## <a name="monitor-share-subscriptions"></a>監視共用訂閱

流覽至 [已傳送的共用]，以查看共用訂閱的狀態-> 共用訂閱]。 這會提供您的資料取用者在接受邀請之後所建立之作用中訂閱的詳細資料。 您可以選取共用訂用帳戶，然後選取 *[撤銷]* ，以停止未來的資料取用者更新。 

## <a name="snapshot-history"></a>快照集歷程記錄 

在共用的 [歷程 **記錄** ] 索引標籤中，您可以查看資料從資料提供者複製到資料取用者的資料存放區的時間。 您可以監視每個快照集的頻率、持續時間和狀態。 

![螢幕擷取畫面顯示 Azure 入口網站中已傳送的共用。](./media/sent-shares.png "快照集歷程記錄") 

您可以按一下 [執行開始日期]，以查看每個快照集執行的更多詳細資料。 然後，按一下每個資料集的狀態，以查看傳輸的資料量、複製的檔案/記錄數目、快照集的持續時間、已使用的虛擬核心數目和錯誤訊息（如果有的話）。 

最多會顯示最多30天的快照記錄。 如果您需要儲存並查看超過30天的歷程記錄，您可以利用診斷設定。

## <a name="diagnostic-setting"></a>診斷設定

您可以設定診斷設定來儲存記錄資料或事件。 流覽至 [監視]-> 診斷設定]，然後選取 [ **新增診斷設定** ]。 選取您感興趣的記錄資料或事件，以及要儲存或傳送的事件。 

![螢幕擷取畫面顯示 Azure 入口網站中的 [診斷設定] 頁面。](./media/diagnostic-settings.png "診斷設定") 

## <a name="next-steps"></a>後續步驟 

深入瞭解 [Azure Data Share 術語](terminology.md)