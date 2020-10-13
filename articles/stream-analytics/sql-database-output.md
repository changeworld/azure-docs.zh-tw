---
title: Azure 串流分析的 Azure SQL Database 輸出
description: 本文說明 Azure 串流分析的 Azure SQL Database 作為輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 4310bd94edd5ebe14eab40b6d19e2bacbdd1b03c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906220"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure 串流分析的 Azure SQL Database 輸出

如果資料的本質上具備關聯性，或者應用程式所需的內容需由關聯式資料庫提供時，您可以使用 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 作為資料輸出。 Azure 串流分析作業會寫入 SQL Database 中的現有資料表。 資料表結構描述必須完全符合作業輸出中的欄位及其類型。 您也可以透過 SQL Database 輸出選項，將 [Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) 指定為輸出。 若要深入了解如何改善寫入輸送量，請參閱[使用 Azure SQL Database 作為輸出的串流分析](stream-analytics-sql-output-perf.md)一文。

您也可以使用 [AZURE SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) 作為輸出。 您必須 [在 SQL 受控執行個體中設定公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) ，然後在 Azure 串流分析中手動設定下列設定。 以附加資料庫執行 SQL Server 的 Azure 虛擬機器，也可以透過手動進行下列設定來支援。

## <a name="output-configuration"></a>輸出設定

下表列出屬性名稱及其描述以建立 SQL Database 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 | 您傳送輸出的目標資料庫名稱。 |
| 伺服器名稱 | 邏輯 SQL server 名稱或受控實例名稱。 若為 SQL 受控執行個體，則必須指定埠3342。 例如，「sampleserver.public.database.windows.net,3342」 |
| 使用者名稱 | 具有資料庫寫入存取權的使用者名稱。 串流分析只支援 SQL 驗證。 |
| 密碼 | 連線到資料庫的密碼。 |
| Table | 要在其中寫入輸出的資料表名稱。 表格名稱會區分大小寫。 這個資料表的結構描述應該完全符合您作業輸出所產生的欄位數目及其類型。 |
|繼承資料分割配置| 可讓您繼承先前查詢步驟的資料分割配置，以便啟用資料表多個寫入器的完全平行拓撲。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。|
|批次計數上限| 隨每筆大量插入交易傳送的建議記錄數上限。|

有兩張介面卡可讓您從 Azure 串流分析輸出到 Azure Synapse Analytics (先前稱為 SQL 資料倉儲)：SQL Database 和 Azure Synapse。 如果下列任何一個條件成立，建議您選擇 Azure Synapse Analytics 介面卡，而不是 SQL Database 的介面卡：

* **輸送量**：如果您現在或未來的預期輸送量大於 10 MB/秒，請使用 Azure Synapse 輸出選項以獲得更好的效能。

* **輸入分割區**：如果您有八個以上的輸入分割區，請使用 Azure Synapse 輸出選項以獲得更好的相應放大。

## <a name="partitioning"></a>資料分割

資料分割必須啟用，而且是以查詢中的 PARTITION BY 子句為基礎。 當 [繼承資料分割] 選項啟用時，會遵循輸入資料分割以進行 [完整可並行的查詢](stream-analytics-scale-jobs.md)。 若要深入了解如何在將資料載入 Azure SQL Database 時達到更佳寫入輸送量效能，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。

## <a name="output-batch-size"></a>輸出批次大小

您可以使用 [ **最大批次計數**] 來設定訊息大小上限。 預設的最大值為10000，預設的最小值為每個單一大量插入100個數據列。 如需詳細資訊，請參閱 [AZURE SQL 限制](../sql-database/sql-database-resource-limits.md)。 每個批次一開始會以上限批次計數大量插入。 批次根據 SQL 的可重試錯誤分割為一半 (直到批次計數下限)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
