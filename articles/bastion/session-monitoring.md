---
title: Azure 堡壘會話監視和管理 |微軟文檔
description: 在本文中，瞭解如何選擇正在進行的會話並強制斷開連接或刪除它。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981084"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 堡壘的會話監視和管理

在虛擬網路中預配和部署 Bastion 服務後，可以使用該服務無縫連接到此虛擬網路中的任何 VM。 當使用者連接到工作負荷時，Azure 堡壘可用於監視遠端會話並採取快速管理操作。 Azure 堡壘會話監視允許您查看哪些使用者連接到哪些 VM。 它顯示使用者連接的 IP、連線時間以及他們連接的時間。 會話管理經驗允許您選擇正在進行的會話，並強制斷開連接或刪除會話，以便斷開使用者與正在進行的會話的連接。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>監視遠端會話

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 Azure 堡壘資源，然後從 Azure 堡壘頁中選擇 **"會話**"。

   ![工作階段](./media/session-monitoring/sessions.png)
2. 在 **"會話"** 頁上，您可以看到右側正在進行的遠端會話。

   ![查看會話](./media/session-monitoring/view-session.png)
3. 選擇 **"刷新"** 以查看更新的遠端會話清單。 選擇"刷新"時，Azure 堡壘將獲取最新的監視資訊並在門戶中刷新資訊。

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 為閘道管理器的入站流量啟用埠 4443，以便會話監視正常工作。
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>刪除或強制斷開正在進行的遠端會話

您可以選擇一組會話並強制斷開它們。 以下步驟演示如何刪除遠端會話：

1. 導航到 Azure 堡壘資源，並從 Azure 堡壘頁面中選擇 **"會話**"。

   ![navigate](./media/session-monitoring/navigate.png)
2. 選擇"會話"後，您將看到遠端會話的清單。

   ![清單會話](./media/session-monitoring/list.png)
3. 選擇特定的遠端會話，然後選擇會話行右側末尾的三個橢圓，然後選擇 **"刪除**"。

   ![delete](./media/session-monitoring/delete.png)
4. 選擇"刪除"時，遠端會話將斷開連接，並且使用者將在遠端會話中顯示"您已中斷連線"消息。

   ![中斷連線](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>後續步驟

閱讀[堡壘常見問題解答](bastion-faq.md)。