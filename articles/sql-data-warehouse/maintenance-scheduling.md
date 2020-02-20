---
title: Azure 維護排程
description: 維護排程可讓客戶規劃必要的排程維護事件，Azure SQL 資料倉儲服務會使用它們來推出新功能、升級與修補程式。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 1cf4cc9cf4d98dfca59e01cc264549af3a4d5cb4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471783"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用維護排程管理服務更新和維護

維護排程功能整合了服務健康狀態規劃的維護通知、資源健康狀態檢查監視器，以及 Azure SQL 資料倉儲維護排程服務。

您應該使用維護排程來選擇時間範圍，以方便接收新的功能、升級和修補程式。 您需要在七天期間內選擇主要和次要維護時段，每個視窗都必須在不同的日期範圍內。

例如，您可以將星期六22:00 的主視窗排程為星期日01:00，然後將星期三19:00 的次要時段排程為22:00。 如果 SQL 資料倉儲無法在主要維護時段執行維護，它將在次要維護時段再次試著進行維護。 服務維護有時可能會在主要和次要時段發生。 為了確保能夠快速完成所有維護作業，DW400c 和較低的資料倉儲層可以在指定的維護期間以外完成維護。

所有新建立的 Azure SQL 資料倉儲執行個體在部署期間都會套用一個系統定義的維護排程。 一旦部署完畢，就可以編輯排程。

雖然維護期間可以介於三到八個小時之間，但這並不表示資料倉儲在持續時間內會離線。 維護可能會在該時段內的任何時間發生，而且您應該預期在該期間的單一中斷連線持續到 5-6 分鐘，因為服務會將新的程式碼部署至您的資料倉儲。 在維護期間，DW400c 和較低可能會在不同的時間發生多個短暫的連線中斷。 當維護開始時，將會取消所有作用中的會話，且不會回復未認可的交易。 若要將執行個體的停機時間降至最低，先確定資料倉儲在您選擇的維護期間之前沒有長時間執行的交易。

除非需要部署時間緊迫的更新，否則所有維護作業都應該在指定的維護期間內完成。 如果您的資料倉儲已在排程維護期間暫停，它將會在繼續作業期間更新。 在您的資料倉儲維護完成後，您會立即收到通知。

## <a name="alerts-and-monitoring"></a>警示和監視

服務健康狀態通知與資源健康狀態檢查監視器的整合，可讓客戶隨時掌握即將進行的維護活動。 這項自動化會利用 Azure 監視器。 您可以決定您想要收到即將進行維護事件通知的方式。 此外，您也可以選擇哪些自動化流程可協助您管理停機時間，並將操作影響降至最低。
24小時預先通知會在所有不屬於 DWC400c 和較低層的維護事件之前。

> [!NOTE]
> 在此事件中，我們必須部署時間緊迫的更新，可能會大幅降低提前通知時間。

如果您收到維護將進行的預先通知，但 SQL 資料倉儲無法在這段期間內執行維護，則您將收到取消通知。 之後，維護將會在下一個排程維護期間繼續。

所有作用中的維護事件都會顯示在 [服務健康狀態 - 計劃性維護] 區段中。 服務健康狀態歷程記錄包含過去事件的完整記錄。 您可以在作用中的事件期間，透過 Azure 服務健康狀態檢查入口網站儀表板來監視維護。

### <a name="maintenance-schedule-availability"></a>維護排程可用性

即使維護排程尚無法在您所選的區域中使用，但您可以隨時檢視和編輯維護排程。 當維護排程在您的區域中變成可用時，已識別的排程將在您的資料倉儲上立即變成作用中。

## <a name="view-a-maintenance-schedule"></a>檢視維護排程 

### <a name="portal"></a>入口網站

根據預設，所有新建立的 Azure SQL 資料倉儲執行個體都擁有已在部署期間套用的八小時主要和次要維護時段。 如上所示，您可以在部署完成後變更 windows。 如果沒有事先通知，就不會在指定的維護時段以外進行維護。

若要檢視已套用到您資料倉儲的維護排程，請完成下列步驟：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  選取您要檢視的資料倉儲。 
3.  所選取的資料倉儲即會在概觀刀鋒視窗上開啟。 套用至資料倉儲的維護排程會顯示在 [**維護排程**] 下方。

![[概觀] 刀鋒視窗](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>變更維護排程 

### <a name="portal"></a>入口網站
維護排程可隨時更新或變更。 如果選取的執行個體正在進行作用中的維護週期，設定將會儲存。 它們將在下一個已識別的維護期間變成作用中。 [深入了解](https://docs.microsoft.com/azure/service-health/resource-health-overview)如何在作用中維護事件期間監視您的資料倉儲。 

### <a name="identifying-the-primary-and-secondary-windows"></a>識別主要與次要時段

主要和次要時段必須有個別的日期範圍。 範例中的主要時段是星期二到星期四，而次要時段為星期六到星期日。

若要變更資料倉儲的維護排程，請完成下列步驟：
1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  選取您要更新的資料倉儲。 頁面會在概觀刀鋒視窗上開啟。 
3.  選取 [總覽] 分頁上的 [**維護排程摘要**] 連結，以開啟維護排程設定的頁面。 或者，選取左側資源功能表上的 [維護排程] 選項。  

    ![概觀刀鋒視窗選項](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 使用頁面頂端的選項，來識別您主要維護時段的偏好日期範圍。 此選取範圍會決定您的主要時段將在週間發生或在週末發生。 您的選取範圍將會更新下拉式清單的值。 在預覽期間，某些區域可能尚未支援可用 [日期] 選項的完整集合。

   ![維護設定刀鋒視窗](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 使用下拉式清單方塊，來選擇您偏好的主要和次要維護時段：
   - **日期**：想要在選取的時段內執行維護的偏好日期。
   - **開始時間**：偏好的維護時段開始時間。
   - **時段**：時段的偏好持續期間。

   刀鋒視窗底部的 [排程摘要] 區域會根據所選取的值來更新。 
  
6. 選取 [儲存]。 隨即出現一則訊息，確認您的新排程目前為作用中。 

   如果您將排程儲存於不支援維護排程的區域中，即會出現下列訊息。 您的設定已儲存，並在功能於您選取的區域中成為可用時變成作用中。    

   ![關於區域可用性的訊息](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>後續步驟
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure 監視器來建立、檢視和管理警示。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)適用於記錄警示規則的 Webhook 動作。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)如何建立及管理動作群組。
- [深入了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服務健康狀態。
