---
title: Azure 堡壘會話監視和管理 |微軟文件
description: 在本文中,瞭解如何選擇正在進行的會話並強制斷開連接或刪除它。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619185"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure 堡壘的作業階段監視和管理

在虛擬網路中預配和部署 Bastion 服務後,可以使用該服務無縫連接到此虛擬網路中的任何 VM。 當使用者連接到工作負荷時,Azure 堡壘可用於監視遠端作業並採取快速管理操作。 Azure 堡壘工作階段監視允許您查看哪些使用者連接到哪些 VM。 它顯示使用者連接的 IP、連接時間以及他們連接的時間。 工作階段管理經驗允許您選擇正在進行的作業階段,並強制斷開連接或刪除工作階段,以便斷開使用者與正在進行的作業階段的連接。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>監察遠端工作階段

1. 在[Azure 門戶](https://portal.azure.com)中,導航到 Azure 堡壘資源,然後從 Azure 堡壘頁中選擇 **「會話**」。。

   ![工作階段](./media/session-monitoring/sessions.png)
2. 在 **"工作階段'** 頁上,您可以看到右側正在進行的遠端作業階段。

   ![檢視工作階段](./media/session-monitoring/view-session.png)
3. 選擇 **「刷新」** 以檢視更新的遠端作業清單。 選擇「刷新」時,Azure 堡壘將獲取最新的監視資訊並在門戶中刷新資訊。

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> 為閘道管理器的入站流量啟用埠 4443,以便會話監視正常工作。
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>移除或強制斷線正在進行的遠端工作階段

您可以選擇一組作業階段並強制斷開它們。 以下步驟展示如何移除遠端工作階段:

1. 導航到 Azure 堡壘資源,並從 Azure 堡壘頁面中選擇 **「會話**」。。

   ![navigate](./media/session-monitoring/navigate.png)
2. 選擇"工作階段"後,您將看到遠端作業階段的清單。

   ![清單工作階段](./media/session-monitoring/list.png)
3. 選擇特定的遠端會話,然後選擇會話行右側末尾的三個橢圓,然後選擇 **"刪除**"。

   ![delete](./media/session-monitoring/delete.png)
4. 選擇"刪除"時,遠端作業階段將斷開連接,並且使用者將在遠端作業中顯示「您已斷開連接」訊息。

   ![中斷連線](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>後續步驟

閱讀[的移除的裝置的錯誤 。](bastion-faq.md)