---
title: 規劃 Azure 維護事件
description: 瞭解如何準備 Azure SQL Database 和 Azure SQL 受控執行個體中規劃的維護事件。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 08/25/2020
ms.openlocfilehash: 4c7b78f14602632068a19d520aeeb940b543be61
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948210"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>規劃 Azure SQL Database 和 Azure SQL 受控執行個體中的 Azure 維護事件
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

瞭解如何在 Azure SQL Database 和 Azure SQL 受控執行個體中為您的資料庫做好規劃的維護事件。

## <a name="what-is-a-planned-maintenance-event"></a>什麼是計劃性維護事件？

為了讓 Azure SQL Database 和 Azure SQL 受控執行個體服務保持安全、符合規範、穩定和效能，您幾乎會不斷地透過服務元件來執行更新。 感謝新式、健全的服務架構和創新的技術（例如 [熱修補](https://aka.ms/azuresqlhotpatching)），大部分的更新在服務可用性方面都是完全透明且不可具影響力的。 但是，有幾種類型的更新會導致短暫的服務中斷，且需要進行特殊處理。 

針對每個資料庫，Azure SQL Database 和 Azure SQL 受控執行個體維護資料庫複本的仲裁，其中一個複本是主要複本。 在任何時候，主要複本都必須是線上服務，而且至少一個次要複本必須狀況良好。 計劃性維護期間，資料庫仲裁的成員每次只會有一個離線，目的是在線上隨時保有一個可回應的主要複本和至少一個次要複本，以確保用戶端不會停機。 主要複本需要離線時，會產生重新設定/容錯移轉程序，這時，其中一個次要複本會成為新的主要複本。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>計劃性維護事件期間的預期情況

維護事件可以產生單一或多個容錯移轉，視維護事件開始時的主要和次要複本的 constellation 而定。 平均而言，每個規劃的維護事件都會發生1.7 容錯移轉。 重新進行/容錯移轉通常會在30秒內完成。 平均是8秒。 如果已連接，您的應用程式必須重新連接到您資料庫的新主要複本。 如果在新的主要複本上線之前，嘗試重新設定資料庫時發生新的連接，您會收到錯誤 40613 (資料庫無法使用) ：*目前無法使用伺服器 ' {servername} ' 上的「資料庫 ' {databasename} '」。請稍後重試連接。* 」 如果您的資料庫有長時間執行的查詢，此查詢將會在重新設定期間中斷，且需要重新開機。

## <a name="how-to-simulate-a-planned-maintenance-event"></a>如何模擬規劃的維護事件

確保您的用戶端應用程式在部署至生產環境之前，能夠復原維護事件，有助於降低應用程式錯誤的風險，並且會對您的終端使用者造成應用程式可用性的影響。 您可以透過 PowerShell、CLI 或 REST API [起始手動容錯移轉](https://aka.ms/mifailover-techblog) ，在規劃的維護事件期間測試用戶端應用程式的行為。 它會產生與維護事件相同的行為，使主要複本離線。

## <a name="retry-logic"></a>重試邏輯

任何連線到雲端資料庫服務的用戶端生產應用程式均應實作健全的連線[重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 這有助於讓使用者無法透明地進行容錯移轉，或至少將負面影響降到最低。

## <a name="resource-health"></a>資源健康情況

如果您的資料庫遇到登入失敗，請檢查[Azure 入口網站](https://portal.azure.com)中的 [[資源健康狀態](../../service-health/resource-health-overview.md#get-started)] 視窗，以取得目前的狀態。 [健康情況歷程記錄] 區段會包含每個事件停止運作的原因 (如果有的話)。

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解 Azure SQL Database 和 Azure SQL 受控執行個體的 [資源健康狀態](resource-health-to-troubleshoot-connectivity.md) 。
- 如需重試邏輯的詳細資訊，請參閱 [暫時性錯誤的重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。
