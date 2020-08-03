---
title: 如何監視 Azure 資料共用
description: 瞭解如何在 Azure 資料共用中監視邀請狀態、共用訂閱和快照記錄
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 2a986afa1916d179834c516a26f76a2789f82452
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511882"
---
# <a name="monitor-azure-data-share"></a>監視 Azure Data Share  

本文說明如何使用 Azure 資料共用來監視您的資料共用。 身為數據提供者，您可以監視資料共用關聯性的各個層面。 詳細資訊，例如您的資料取用者是否已接受您對資料共用的邀請，以及他們是否已建立共用訂閱並開始使用您的資料，都可供監視。 

身為數據取用者，您可以監視已觸發至您的 Azure 訂用帳戶的快照集。 

## <a name="monitor-invitation-status"></a>監視邀請狀態

流覽至 [已傳送的共用-> 邀請]，以查看資料共用邀請的狀態。 

![邀請狀態](./media/invitation-status.png "邀請狀態") 

您的邀請可以有三種狀態：

* 待決-資料共用收件者尚未接受邀請。
* 已接受-資料共用收件者已接受邀請。
* 已拒絕-資料共用收件者已拒絕邀請。

> [!IMPORTANT]
> 如果您在已接受邀請之後將其刪除，則不等於撤銷存取權。 如果您想要停止將未來的快照集複製到資料取用者儲存體帳戶，您必須透過 [*共用訂閱*] 索引標籤撤銷存取權。 

## <a name="monitor-share-subscriptions"></a>監視共用訂閱

流覽至 [已傳送的共用]-> 共用訂閱]，以查看共用訂閱的狀態。 這會提供您在接受邀請之後，您的資料取用者所建立之作用中訂閱的詳細資料。 您可以選取共用訂用帳戶，然後選取 *[撤銷]*，以停止資料取用者的未來更新。 

## <a name="snapshot-history"></a>快照集歷程記錄 

在共用的 [歷程**記錄**] 索引標籤中，您可以查看何時將資料從資料提供者複製到資料取用者的資料存放區。 您可以監視每個快照的頻率、持續時間和狀態。 

![快照集歷程記錄](./media/sent-shares.png "快照集歷程記錄") 

按一下 [執行] 開始日期，即可查看每個快照集執行的更多詳細資料。 然後按一下每個資料集的狀態，以查看傳輸的資料量、複製的檔案/記錄數目、快照集的持續時間、已使用的虛擬核心數目和錯誤訊息（如果有的話）。 

最多會顯示30天的快照集歷程記錄。 如果您需要儲存並看到超過30天的歷程記錄，您可以利用診斷設定。

## <a name="diagnostic-setting"></a>診斷設定

您可以設定診斷設定來儲存記錄資料或事件。 流覽至 [監視]-> [診斷設定]，然後選取 [**新增診斷設定**]。 選取您感興趣的記錄檔資料，以及您要儲存或傳送的事件。 

![快照集歷程記錄](./media/diagnostic-settings.png "診斷設定") 

## <a name="next-steps"></a>後續步驟 

深入瞭解[Azure 資料共用術語](terminology.md)