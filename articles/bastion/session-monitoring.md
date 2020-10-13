---
title: Azure Bastion 工作階段監視和管理 | Microsoft Docs
description: 在此文章中，了解如何選取進行中的工作階段，並將其強制中斷連線或刪除。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980732"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>適用於 Azure Bastion 的工作階段監視和管理

在虛擬網路中佈建並部署 Bastion 服務之後，您就可以使用該服務，順暢地連線到此虛擬網路中的任何 VM。 當使用者連線到工作負載時，就能使用 Azure Bastion 來監視遠端工作階段，並採取快速管理動作。 Azure Bastion 工作階段監視可讓您檢視哪些使用者已連線到哪些 VM。 其會顯示使用者連線的來源 IP、已連線的時間長度及其何時連線。 工作階段管理體驗可讓您選取進行中的工作階段，並強制中斷連線或刪除工作階段，以中斷使用者與進行中工作階段的連線。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>監視遠端工作階段

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 Azure Bastion 資源，然後從 Azure Bastion 頁面中選取 [工作階段]。

   ![螢幕擷取畫面顯示已選取會話的 Azure 入口網站功能表設定。](./media/session-monitoring/sessions.png)
2. 在 [工作階段] 頁面上，您可以在右側看到進行中的遠端工作階段。

   ![檢視工作階段](./media/session-monitoring/view-session.png)
3. 選取 [重新整理] 以查看遠端工作階段的更新清單。 當您選取 [重新整理] 時，Azure Bastion 將擷取最新的監視資訊，並在入口網站中重新整理。

   ![螢幕擷取畫面顯示使用 Azure 防禦的會話，並反白顯示重新整理選項。](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>刪除或強制中斷連線進行中的遠端工作階段

您可以選取一組工作階段，並強制中斷其連線。 下列步驟說明如何刪除遠端工作階段：

1. 瀏覽至您的 Azure Bastion 資源，然後從 Azure Bastion 頁面中選取 [工作階段]。

   ![螢幕擷取畫面顯示在 [設定] 下選取會話的 Azure 入口網站。](./media/session-monitoring/navigate.png)
2. 選取 [工作階段] 之後，您會看到遠端工作階段的清單。

   ![列出工作階段](./media/session-monitoring/list.png)
3. 選取特定的遠端工作階段，接著選取工作階段資料列最右側的三個省略號，然後選取 [刪除]。

   ![螢幕擷取畫面顯示已選取 [刪除] 圖示的 Azure 入口網站顯示會話。](./media/session-monitoring/delete.png)
4. 當您選取 [刪除] 時，遠端工作階段將會中斷連線，而使用者將在遠端工作階段中顯示「您已經斷線」的訊息。

   ![螢幕擷取畫面顯示驗證您已中斷連線的訊息。](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>後續步驟

閱讀 [Bastion 常見問題集](bastion-faq.md)。
