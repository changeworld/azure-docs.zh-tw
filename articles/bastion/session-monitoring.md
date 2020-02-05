---
title: Azure 防禦會話監視和管理 |Microsoft Docs
description: 在本文中，您將瞭解如何選取進行中的會話，並強制中斷連線或將它刪除。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76981084"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 防禦的會話監視和管理

在虛擬網路中布建並部署防禦服務之後，您就可以使用它來順暢地連線到此虛擬網路中的任何 VM。 當使用者連線到工作負載時，您可以使用 Azure 防禦來監視遠端會話，並採取快速的管理動作。 「Azure 防禦會話監視」可讓您查看哪些使用者已連線到哪些 Vm。 它會顯示使用者所連線的 IP、已連線的時間長度，以及它們何時連線。 會話管理體驗可讓您選取進行中的會話，並強制中斷連線或刪除會話，以中斷使用者與進行中會話的連線。

## <a name="monitor"></a>監視遠端會話

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的 azure 防禦資源，然後從 Azure 防禦頁面選取 **會話**]。

   ![工作階段](./media/session-monitoring/sessions.png)
2. 在 [**會話**] 頁面上，您可以看到在右側進行的遠端會話。

   ![查看會話](./media/session-monitoring/view-session.png)
3. 選取 **[** 重新整理] 以查看已更新的遠端會話清單。 當您選取 [重新整理] 時，Azure 防禦會提取最新的監視資訊，並在入口網站中重新整理。

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 針對來自閘道管理員的輸入流量啟用埠4443，讓會話監視能夠正常執行。
>

## <a name="view"></a>刪除或強制中斷連線進行中的遠端會話

您可以選取一組會話，並強制中斷其連線。 下列步驟說明如何刪除遠端會話：

1. 流覽至您的 Azure 防禦資源，然後從 Azure 防禦頁面選取 [**會話**]。

   ![定位](./media/session-monitoring/navigate.png)
2. 選取 [會話] 之後，您會看到遠端會話的清單。

   ![列出會話](./media/session-monitoring/list.png)
3. 選取特定的遠端會話，然後選取 [會話] 資料列右側的三個省略號，然後選取 [**刪除**]。

   ![delete](./media/session-monitoring/delete.png)
4. 當您選取 [刪除] 時，遠端會話將會中斷連線，而使用者會在遠端會話中顯示「您已中斷連線」的訊息。

   ![取消](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)。