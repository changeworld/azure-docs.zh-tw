---
title: 在專用的 SQL 集區中使用 Azure 串流分析
description: 使用 azure 串流分析搭配 Azure Synapse 中的專用 SQL 集區來開發即時解決方案的秘訣。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "96458222"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中搭配使用 Azure 串流分析與專用的 SQL 集區

Azure 串流分析是完全受控的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 如需基本概念，請參閱 [Azure 串流分析簡介](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 您可以接著依照 [開始使用 Azure 資料流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 教學課程，了解如何使用資料流分析建立端對端解決方案。

在本文中，您將瞭解如何使用專用的 SQL 集區作為輸出接收，以使用 Azure 串流分析作業來內嵌高輸送量資料。

## <a name="prerequisites"></a>必要條件

* Azure 串流分析作業-若要建立 Azure 串流分析作業，請遵循 [開始使用 Azure 串流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 教學課程中的步驟，以：  

    1. 建立事件中樞輸入
    2. 設定並啟動事件產生器應用程式
    3. 佈建資料流分析工作
    4. 指定工作輸入和查詢
* 專用的 SQL 集區-若要建立新的專用 SQL 集區，請遵循 [快速入門：建立專用的 sql 集](../quickstart-create-sql-pool-portal.md)區中的步驟。

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>指定串流輸出以指向您專用的 SQL 集區

### <a name="step-1"></a>步驟 1

在 Azure 入口網站中，移至您的串流分析作業，然後按一下 [**作業拓撲**] 功能表底下的 [**輸出**]。

### <a name="step-2"></a>步驟 2

按一下 [ **新增** ] 按鈕，然後從下拉式功能表中選擇 [ **Azure Synapse Analytics** ]。

![選擇 Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>步驟 3

輸入下列值：

* *輸出別名*：輸入此作業輸出的易記名稱。
* *訂閱*：
  * 如果您專用的 SQL 集區與串流分析作業位於相同的訂用帳戶中，請按一下 [**從您的訂用帳戶選取 Azure Synapse Analytics**]。
  _ 如果您專用的 SQL 集區位於不同的訂用帳戶中，請按一下 [以手動方式提供 Azure Synapse Analytics 設定]。
* *資料庫*：從下拉式清單中選取目的地資料庫。
* 使用者名稱：指定具有資料庫寫入權限的帳戶的使用者名稱。
* 密碼：提供指定之使用者帳戶的密碼。
* 資料表：指定資料庫中目標資料表的名稱。
* 按一下 [ **儲存** ] 按鈕

![完成 Azure Synapse Analytics 表單](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>步驟 4

在您可以執行測試之前，您必須在專用的 SQL 集區中建立資料表。  使用 SQL Server Management Studio (SSMS) 或您選擇的查詢工具來執行下列資料表建立腳本。

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

在串流分析作業的 Azure 入口網站上，按一下您的作業名稱。  在 [_*_輸出詳細資料_*_] 窗格中，按一下 [*_測試_*] 按鈕。

![輸出詳細資料的測試按鈕 ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) 當資料庫的連接成功時，您會在入口網站中看到通知。

### <a name="step-6"></a>步驟 6

按一下 [_*_作業拓撲_*_] 下的 [_*_查詢_*_] 功能表，然後變更查詢，以將資料插入您所建立的資料流程輸出。  按一下 [ _*_測試選取的查詢_*_ ] 按鈕來測試您的查詢。  當您的查詢測試成功時，請按一下 [ _*_儲存查詢_*_ ] 按鈕。

![儲存查詢](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>步驟 7

啟動 Azure 串流分析作業。  按一下 [_*_總覽_*_] 功能表上的 [_*_開始_*_] 按鈕。

![啟動串流分析作業](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

按一下 [開始工作] 窗格上的 _ [*_開始_*] 按鈕。

![按一下 [開始]](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>後續步驟

如需整合的總覽，請參閱 [整合其他服務](sql-data-warehouse-overview-integrate.md)。
如需更多開發秘訣，請參閱 [專用 SQL 集區的設計決策和程式碼撰寫技術](sql-data-warehouse-overview-develop.md)。
