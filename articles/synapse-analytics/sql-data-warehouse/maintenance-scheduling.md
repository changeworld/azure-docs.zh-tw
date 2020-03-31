---
title: Synapse SQL 池的維護計畫
description: 維護計畫使客戶能夠圍繞 Azure Synapse Analytics 用於推出新功能、升級和修補程式所需的計畫維護事件進行規劃。
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f193580ca03d4b1805f3c044658a34f468f3f44f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346551"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用維護排程管理服務更新和維護

維護計畫功能集成了 Azure Synapse 分析中的 Synapse SQL 池（資料倉儲）的服務運行狀況計畫維護通知、資源運行狀況檢查監視器和維護計畫服務。 

在方便接收新功能、升級和修補程式時，應使用維護計畫來選擇時間視窗。 您需要在 7 天內選擇主維護視窗和輔助維護視窗，每個視窗必須在單獨的天範圍內。

例如，您可以安排星期六 22：00 到周日的 01：00 的主視窗，然後安排星期三 19：00 到 22：00 的次要視窗。 如果在主維護時段無法執行維護，它將在輔助維護時段內重試維護。 服務維護有時可能發生在主視窗和次要視窗期間。 為了確保快速完成所有維護操作，DW400c 和較低的資料倉儲層可以在指定的維護視窗之外完成維護。

所有新創建的資料倉儲實例都將在部署期間應用系統定義的維護計畫。 一旦部署完畢，就可以編輯排程。

儘管維護視窗可能介於 3 到 8 小時之間，但這並不意味著資料倉儲將在持續時間內離線。 維護可以在該視窗的任何時間進行，並且當服務將新代碼部署到資料倉儲時，您應該期望在此期間進行一次斷開連接，持續 5-6 分鐘。 DW400c 和更低級別在維護時段期間不同時間可能會遭受多個連接短暫損失。 維護開始時，將取消所有活動會話，並將回滾未提交的事務。 若要將執行個體的停機時間降至最低，先確定資料倉儲在您選擇的維護期間之前沒有長時間執行的交易。

除非我們需要部署時間敏感的更新，否則所有維護操作都應在指定的維護時段內完成。 如果您的資料倉儲已在排程維護期間暫停，它將會在繼續作業期間更新。 資料倉儲維護完成後，將立即通知您。

## <a name="alerts-and-monitoring"></a>警示和監視

服務健康狀態通知與資源健康狀態檢查監視器的整合，可讓客戶隨時掌握即將進行的維護活動。 此自動化利用了 Azure 監視器。 您可以決定您想要收到即將進行維護事件通知的方式。 此外，您還可以選擇哪些自動化流將説明您管理停機時間並最大限度地減少操作影響。

24 小時提前通知先于 DWC400c 和較低層未發生的所有維護事件。

> [!NOTE]
> 如果我們需要部署時間重大更新，高級通知時間可能會顯著縮短。

如果您事先收到將進行維護的通知，但在通知的時間段內無法執行維護，您將收到取消通知。 之後，維護將會在下一個排程維護期間繼續。

所有作用中的維護事件都會顯示在 [服務健康狀態 - 計劃性維護]**** 區段中。 服務健康狀態歷程記錄包含過去事件的完整記錄。 您可以在作用中的事件期間，透過 Azure 服務健康狀態檢查入口網站儀表板來監視維護。

### <a name="maintenance-schedule-availability"></a>維護排程可用性

即使維護排程尚無法在您所選的區域中使用，但您可以隨時檢視和編輯維護排程。 當維護計畫在您所在區域可用時，已識別的計畫將立即在 Synapse SQL 池上處於活動狀態。

## <a name="view-a-maintenance-schedule"></a>檢視維護排程 

預設情況下，所有新創建的資料倉儲實例在部署期間都應用了 8 小時主維護和輔助維護視窗。 如上所述，您可以在部署完成後更改視窗。 如果沒有事先通知，就不會在指定的維護時段以外進行維護。

要查看已應用於 Synapse SQL 池的維護計畫，請完成以下步驟：

1.    登錄到 Azure[門戶](https://portal.azure.com/)。
2.    選擇要查看的 Synapse SQL 池。 
3.    選定的 Synapse SQL 池將在概述邊欄選項卡上打開。 應用於資料倉儲的維護計畫顯示在**維護計畫**下方。

![[概觀] 刀鋒視窗](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>變更維護排程 

維護排程可隨時更新或變更。 如果選取的執行個體正在進行作用中的維護週期，設定將會儲存。 它們將在下一個已識別的維護期間變成作用中。 [深入了解](../../service-health/resource-health-overview.md)如何在作用中維護事件期間監視您的資料倉儲。 

## <a name="identifying-the-primary-and-secondary-windows"></a>識別主要與次要時段

主要和次要時段必須有個別的日期範圍。 範例中的主要時段是星期二到星期四，而次要時段為星期六到星期日。

要更改 Synapse SQL 池的維護計畫，請完成以下步驟：
1.    登錄到 Azure[門戶](https://portal.azure.com/)。
2.    選擇要更新的 Synapse SQL 池。 頁面會在概觀刀鋒視窗上開啟。 
3.    通過選擇概覽邊欄選項卡上的**維護計畫摘要**連結，打開頁面以進行維護計畫設置。 或者，選取左側資源功能表上的 [維護排程]**** 選項。  

    ![概觀刀鋒視窗選項](./media/maintenance-scheduling/maintenance-change-option.png)

4. 使用頁面頂端的選項，來識別您主要維護時段的偏好日期範圍。 此選取範圍會決定您的主要時段將在週間發生或在週末發生。 您的選取範圍將會更新下拉式清單的值。 在預覽期間，某些區域可能尚未支援可用 [日期]**** 選項的完整集合。

   ![維護設定刀鋒視窗](./media/maintenance-scheduling/maintenance-settings-page.png)

5. 使用下拉式清單方塊，來選擇您偏好的主要和次要維護時段：
   - **日期**：想要在選取的時段內執行維護的偏好日期。
   - **開始時間**：偏好的維護時段開始時間。
   - **時段**：時段的偏好持續期間。

   刀鋒視窗底部的 [排程摘要]**** 區域會根據所選取的值來更新。 
  
6. 選取 [儲存]****。 隨即出現一則訊息，確認您的新排程目前為作用中。 

   如果您將排程儲存於不支援維護排程的區域中，即會出現下列訊息。 您的設定已儲存，並在功能於您選取的區域中成為可用時變成作用中。    

   ![關於區域可用性的訊息](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>後續步驟
- [深入了解](../../azure-monitor/platform/alerts-metric.md)如何使用 Azure 監視器來建立、檢視和管理警示。
- [深入了解](../..//azure-monitor/platform/alerts-log-webhook.md)適用於記錄警示規則的 Webhook 動作。
- [深入了解](../..//azure-monitor/platform/action-groups.md)如何建立及管理動作群組。
- [深入了解](../../service-health/service-health-overview.md) Azure 服務健康狀態。
