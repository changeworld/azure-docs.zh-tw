---
title: 使用 Azure 流分析
description: 有關在 Azure Synapse 中將資料倉儲使用 Azure 流分析以開發即時解決方案的提示。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350428"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>將 Azure 流分析與 Azure 突觸分析一起使用

Azure 串流分析是完全受控的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 如需基本概念，請參閱 [Azure 串流分析簡介](../../stream-analytics/stream-analytics-introduction.md)。 您可以接著依照 [開始使用 Azure 資料流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) 教學課程，了解如何使用資料流分析建立端對端解決方案。

在本文中，您將學習如何將資料倉儲用作 Azure 流分析作業的輸出接收器。

## <a name="prerequisites"></a>Prerequisites

* Azure 流分析作業 - 要創建 Azure 流分析作業，請按照["開始使用 Azure 流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md)"教程中的步驟操作：  

    1. 建立事件中樞輸入
    2. 設定並啟動事件產生器應用程式
    3. 佈建資料流分析工作
    4. 指定工作輸入和查詢
* Azure 同步 SQL 池資料倉儲 - 要創建新的資料倉儲，請按照["快速入門"中的步驟創建新的資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)。

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>指定流輸出以指向資料倉儲

### <a name="step-1"></a>步驟 1

從 Azure 門戶轉到流分析作業，然後按一下**作業拓撲**功能表下的 **"輸出**"。

### <a name="step-2"></a>步驟 2

按一下"**添加**"按鈕，並從下拉式功能表中選擇**SQL 資料庫**。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>步驟 3

輸入下列值：

* *輸出別名*：輸入此作業輸出的易記名稱。
* *訂閱*：
  * 如果資料倉儲與流分析作業處於同一訂閱，請按一下從***訂閱中選擇 SQL 資料庫***。
  * 如果您的資料庫處於其他訂閱中，請按一下手動"提供 SQL 資料庫"設置。
* *資料庫*：從下拉清單中選擇目標資料庫。
* 使用者名稱**：指定具有資料庫寫入權限的帳戶的使用者名稱。
* 密碼**：提供指定之使用者帳戶的密碼。
* 資料表**：指定資料庫中目標資料表的名稱。
* 按一下"**保存"** 按鈕

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>步驟 4

在運行測試之前，您需要在資料倉儲中創建表。  使用 SQL 伺服器管理工作室 （SSMS） 或您選擇的查詢工具運行以下表創建腳本。

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>步驟 5

在"流分析"作業的 Azure 門戶上，按一下作業名稱。  按一下"***輸出詳細資訊***"窗格中的 ***"測試***"按鈕。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)當與資料庫的連接成功時，您將在門戶中看到通知。

### <a name="step-6"></a>步驟 6

按一下***作業拓撲***下的 ***"查詢"*** 功能表，然後更改查詢以將資料插入到您創建的 Stream 輸出中。  按一下"***測試所選查詢***"按鈕以測試查詢。  查詢測試成功時，按一下"***保存查詢***"按鈕。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>步驟 7

啟動 Azure 流分析作業。  按一下 ***"概述"*** 功能表上的 ***"開始"*** 按鈕。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

按一下"開始作業"窗格上的 ***"開始"*** 按鈕。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>後續步驟

有關集成的概述，請參閱[集成其他服務](sql-data-warehouse-overview-integrate.md)。
有關更多開發提示，請參閱[資料倉儲的設計決策和編碼技術](sql-data-warehouse-overview-develop.md)。
