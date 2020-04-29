---
title: 使用 Azure 串流分析
description: 使用 Azure 串流分析搭配 Azure Synapse 中的資料倉儲來開發即時解決方案的秘訣。
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
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633288"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>使用 Azure 串流分析搭配 Azure Synapse 分析

Azure 串流分析是完全受控的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 如需基本概念，請參閱 [Azure 串流分析簡介](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 您可以接著依照 [開始使用 Azure 資料流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 教學課程，了解如何使用資料流分析建立端對端解決方案。

在本文中，您將瞭解如何使用資料倉儲做為 Azure 串流分析作業的輸出接收。

## <a name="prerequisites"></a>先決條件

* Azure 串流分析作業-若要建立 Azure 串流分析作業，請遵循[開始使用 azure 串流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)教學課程中的步驟來：  

    1. 建立事件中樞輸入
    2. 設定並啟動事件產生器應用程式
    3. 佈建資料流分析工作
    4. 指定工作輸入和查詢
* Azure Synapse SQL 集區資料倉儲-若要建立新的資料倉儲，請依照快速入門中的步驟[來建立新的資料倉儲](create-data-warehouse-portal.md)。

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>指定串流輸出以指向您的資料倉儲

### <a name="step-1"></a>步驟 1

從 [Azure 入口網站] 移至您的串流分析作業，然後按一下 [**作業拓撲**] 功能表底下的 [**輸出**]。

### <a name="step-2"></a>步驟 2

按一下 [**新增**] 按鈕，然後從下拉式功能表中選擇 [ **SQL Database** ]。

![選擇 SQL Database](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>步驟 3

輸入下列值：

* *輸出別名*：輸入此工作輸出的易記名稱。
* *訂閱*：
  * 如果您的資料倉儲與串流分析作業位於相同的訂用帳戶中，請按一下 [***從訂閱選取 SQL Database***]。
  * 如果您的資料庫位於不同的訂用帳戶中，請按一下 [手動提供 SQL Database 設定]。
* *資料庫*：從下拉式清單中選取目的地資料庫。
* 使用者名稱**：指定具有資料庫寫入權限的帳戶的使用者名稱。
* 密碼**：提供指定之使用者帳戶的密碼。
* 資料表**：指定資料庫中目標資料表的名稱。
* 按一下 [**儲存**] 按鈕

![已完成 SQL Database 表單](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>步驟 4

在執行測試之前，您必須先在資料倉儲中建立資料表。  使用 SQL Server Management Studio （SSMS）或您選擇的查詢工具來執行下列資料表建立腳本。

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

在串流分析作業的 [Azure 入口網站上，按一下您的作業名稱。  按一下 [***輸出詳細資料***] 窗格中的 [***測試***] 按鈕。

![[測試] 按鈕輸出](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)詳細資料當資料庫連線成功時，您會在入口網站中看到通知。

### <a name="step-6"></a>步驟 6

按一下 [***作業拓撲***] 底下的 [***查詢***] 功能表，然後將查詢變更為將資料插入您建立的資料流程輸出。  按一下 [***測試選取的查詢***] 按鈕，以測試您的查詢。  當您的查詢測試成功時，按一下 [***儲存查詢***] 按鈕。

![儲存查詢](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>步驟 7

啟動 Azure 串流分析作業。  按一下 [***總覽***] 功能表上的 [***開始***] 按鈕。

![啟動串流分析作業](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

按一下 [啟動作業] 窗格上的 [***啟動***] 按鈕。

![按一下 [開始]](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>後續步驟

如需整合的總覽，請參閱[整合其他服務](sql-data-warehouse-overview-integrate.md)。
如需更多開發秘訣，請參閱[資料倉儲的設計決策和程式碼撰寫技術](sql-data-warehouse-overview-develop.md)。
