---
title: 適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) -排程維護-Azure 入口網站
description: 瞭解如何從 Azure 入口網站設定適用於 MySQL 的 Azure 資料庫彈性伺服器的排程維護設定。
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: c8251eb2a89a7481ebc981f2b89668c363651b39
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315009"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>管理適用於 MySQL 的 Azure 資料庫-彈性伺服器的排程維護設定
 
您可以為 Azure 訂用帳戶中的每個彈性伺服器指定維護選項。 選項包括即將進行和已完成之維護事件的維護排程和通知設定。

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫彈性的伺服器處於預覽階段。
 
## <a name="prerequisites"></a>Prerequisites
若要完成本操作說明指南，您需要：
- [適用於 MySQL 的 Azure 資料庫彈性的伺服器](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>指定維護排程選項
 
1. 在 [MySQL 伺服器] 頁面的 [ **設定** ] 標題下，選擇 [ **維護** ] 以開啟排程維護選項。
2. 預設 (系統管理的) 排程是一周的隨機日子，而在晚上11點和排列7am-7pm 本機伺服器時間之間，則會開始維護60分鐘的時間範圍。 如果您想要自訂此排程，請選擇 [ **自訂排程**]。 然後，您可以選取一周的慣用日期，以及一個60分鐘的時間範圍來進行維護開始時間。
 
## <a name="notifications-about-scheduled-maintenance-events"></a>排程維護事件的相關通知
 
您可以使用 Azure 服務健康狀態來 [查看](../../service-health/service-notifications.md) 即將推出的通知，並在彈性的伺服器上執行排程的維護。 您也可以在 Azure 服務健康狀態中 [設定](../../service-health/resource-health-alert-monitor-guide.md) 警示，以取得有關維護事件的通知。
 
## <a name="next-steps"></a>後續步驟  
 
* 瞭解 [適用於 MySQL 的 Azure 資料庫-彈性伺服器中的排程維護](concepts-maintenance.md)
* [Azure 服務健康狀態](../../service-health/overview.md)的相關資訊
