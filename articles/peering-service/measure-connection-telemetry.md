---
title: 'Azure 對等互連服務：如何測量連線遙測 '
description: 在本教學課程中，您將了解如何測量連線遙測。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022491"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>教學課程：測量對等互連服務連線遙測

 在本教學課程中，您將了解如何測量對等互連服務連線的遙測。
 
 連線遙測會提供針對客戶位置與 Microsoft 網路之間的連線所收集的深入解析。 在本文中，您將了解如何檢視特定 Azure 對等互連服務連線的延遲報告。 

若要測量對等互連服務連線遙測，您必須在 Azure 入口網站中註冊對等互連服務連線。 若要了解如何註冊連線，請參閱[註冊對等互連服務連線 - Azure 入口網站](azure-portal.md)。


## <a name="view-a-latency-report"></a>檢視延遲報告

若要檢視特定對等互連服務連線的延遲報告，請遵循下列步驟。

1. 在左窗格中選取 [所有資源]，然後選取對等互連服務連線。 然後選取 [字首] 下的 [開啟]。 

   ![選取對等互連服務連線](./media/peering-service-measure/peering-service-measure-menu.png)

2. 隨即會顯示與該對等互連服務連線相關聯的所有字首的延遲報告頁面。 

      ![延遲報告頁面](./media/peering-service-measure/peering-service-latency-report.png)

3. 根據預設，針對此頁面上顯示的報告，報告會每小時更新一次。 若要檢視不同時間軸的報告，請從 [顯示資料，始於過去] 選擇適當的選項。 

4. 若要檢視特定字首的事件，請選取字首名稱，然後在左窗格中選取 [字首事件]。 已擷取的事件隨即顯示。


   ![字首事件](./media/peering-service-measure/peering-service-prefix-event.png)

 在 [字首事件] 清單中所擷取到的部分可能事件如下所示。

| **字首事件** | **事件類型**|**理由**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |資訊|已收到字首公告|
| PrefixWithdrawalEvent|警告| 已收到字首撤銷 |
| PrefixBackupRouteAnnouncementEvent |資訊|已收到字首備份路由公告 |
| PrefixBackupRouteWithdrawalEvent|警告|已收到字首備份路由撤銷 |
| PrefixActivePath |資訊| 目前的字首作用中路由   |
| PrefixBackupPath | 資訊|目前的字首備份路由   |
| PrefixOriginAsChangeEvent|重大| 已收到具有不同原始自發系統編號 (適用於作用中路由) 的確切字首| 
| PrefixBackupRouteOriginAsChangeEvent  | 錯誤|已收到具有不同原始自發系統編號 (適用於備份路由) 的字首  |

## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。