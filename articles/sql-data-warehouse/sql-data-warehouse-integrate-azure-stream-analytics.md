---
title: 使用 Azure 串流分析
description: 搭配使用 Azure 串流分析與 SQL 資料倉儲以便開發解決方案的秘訣。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721195"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>使用 Azure 串流分析搭配 Azure Synapse 分析
Azure 串流分析是完全受控的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 如需基本概念，請參閱 [Azure 串流分析簡介](../stream-analytics/stream-analytics-introduction.md)。 您可以接著依照 [開始使用 Azure 資料流分析](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 教學課程，了解如何使用資料流分析建立端對端解決方案。

在本文中，您將瞭解如何使用資料倉儲資料庫做為串流分析作業的輸出接收。

## <a name="prerequisites"></a>必要條件
首先，執行 [開始使用 Azure 資料流分析](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 教學課程的下列步驟。  

1. 建立事件中樞輸入
2. 設定並啟動事件產生器應用程式
3. 佈建資料流分析工作
4. 指定工作輸入和查詢

接著，建立 Azure SQL 資料倉儲資料庫

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>指定工作輸出：Azure SQL 資料倉儲資料庫
### <a name="step-1"></a>步驟 1
在您的串流分析作業中，按一下頁面頂端的 [**輸出**]，然後按一下 [**新增**]。

### <a name="step-2"></a>步驟 2
選取 [SQL Database]。

### <a name="step-3"></a>步驟 3
在下一頁輸入下列值：

* *輸出別名*：輸入此工作輸出的易記名稱。
* 訂用帳戶：
  * 如果 SQL 資料倉儲資料庫是在與此資料流分析工作相同的訂用帳戶中，則請選取 [使用目前訂用帳戶的 SQL Database]。
  * 如果您的資料庫是在不同的訂用帳戶中，請選取 [使用其他訂用帳戶的 SQL Database]。
* 資料庫：指定目的地資料庫的名稱。
* 伺服器名稱：為您剛指定的資料庫指定伺服器名稱。 您可以使用 Azure 入口網站進行搜尋。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* 使用者名稱：指定具有資料庫寫入權限的帳戶的使用者名稱。
* 密碼：提供指定之使用者帳戶的密碼。
* 資料表：指定資料庫中目標資料表的名稱。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>步驟 4
按一下核取按鈕以新增此工作輸出，並確認串流分析可成功連接到資料庫。

當資料庫的連接成功時，您會在入口網站中看到通知。 您可以按一下 [測試] 來測試與資料庫的連接。

## <a name="next-steps"></a>後續步驟
如需整合的總覽，請參閱[整合其他服務](sql-data-warehouse-overview-integrate.md)。
如需更多開發秘訣，請參閱[資料倉儲的設計決策和程式碼撰寫技術](sql-data-warehouse-overview-develop.md)。

