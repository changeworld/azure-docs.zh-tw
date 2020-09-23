---
title: 排程維護-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中的排程維護功能。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 35e5e688f5589c23c7c583c7bcbca3fd0956eec7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933892"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器中的排程維護
 
適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器會執行定期維護，讓您的受控資料庫保持安全、穩定且最新。 在維護期間，伺服器會取得新的功能、更新和修補程式。
 
> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器處於預覽階段
 
## <a name="selecting-a-maintenance-window"></a>選取維護視窗
 
您可以在一周的特定日子和該日內的時間範圍內排程維護。 或者，您可以讓系統自動為您挑選一天的時間和時間範圍。 無論何種方式，系統都會在執行任何維護之前的五天警示您。 系統也會讓您知道何時啟動維護，以及成功完成的時間。
 
即將進行排程維護的相關通知可以是：
 
* 以電子郵件傳送至特定位址
* 以電子郵件傳送給 Azure Resource Manager 角色
*  (SMS) 傳送至行動裝置的文字訊息
* 以通知的形式推送至 Azure 應用程式
* 以語音訊息形式傳遞
 
指定維護排程的喜好設定時，您可以挑選星期幾和時間範圍。 若未指定，系統將會依據您伺服器的區域時間挑選晚上 11 點和早上 7 點之間的時間。 您可以為 Azure 訂用帳戶中的每個彈性伺服器定義不同的排程。 
 
> [!IMPORTANT]
> 一般來說，伺服器的成功排程維護事件至少會有 30 天的間隔。
>
> 不過，如果有重大緊急更新 (例如嚴重的弱點)，通知時間範圍可能會縮短至五天以內。 即使在過去 30 天內曾執行過成功的排程維護，重大更新也可能會套用至您的伺服器。

您可以隨時更新排程設定。 如果已為您的彈性伺服器排程維護，而您更新排程喜好設定，則目前的事件將會依排程繼續進行，而且排程設定變更將會在成功完成時生效。 

如果系統取消維護事件或無法順利完成，系統將會分別建立有關已取消或失敗之維護事件的通知。 下次嘗試執行維護時，將會根據目前的排程設定排程，您將會在五天前收到通知。
 
## <a name="next-steps"></a>下一步
 
* 瞭解如何 [變更維護排程](how-to-maintenance-portal.md)
* 瞭解如何使用 Azure 服務健康狀態來[取得即將進行維護的相關通知](/azure/service-health/service-notifications.md)
* 瞭解如何 [設定即將進行之排程維護事件的相關警示](/azure/service-health/resource-health-alert-monitor-guide.md)
