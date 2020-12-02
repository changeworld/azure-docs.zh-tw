---
title: " (先前的 SQL DW) 常見問題的專用 SQL 集區"
description: 本文列出客戶和開發人員 Azure Synapse Analytics 中的專用 SQL 集區 (先前的 SQL DW) 的常見問題。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460514"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics 常見問題中的專用 SQL 集區 (先前的 SQL DW) 

## <a name="general"></a>一般

Q. 什麼是 Azure Synapse？

A. Azure Synapse 是一項分析服務，可將資料倉儲和大型資料分析整合在一起。 Azure Synapse 將這兩個世界結合在一起，以針對 BI 和機器學習需求內嵌、準備、管理和提供資料。 如需詳細資訊，請參閱 [Azure Synapse Analytics 的內容](sql-data-warehouse-overview-what-is.md)。

Q. Azure SQL 資料倉儲發生什麼事？

A. Azure Synapse 是 Azure SQL 資料倉儲的演進。 我們採用相同產業領先的資料倉儲，以提供全新的效能和功能層級。 您可以繼續在生產環境中使用專用的 SQL 集區來執行現有的資料倉儲工作負載， (先前在 Azure Synapse 中的 SQL DW) 。 如需詳細資訊，請參閱[什麼是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

Q. 什麼是在 Azure Synapse Analytics 中 (先前為 SQL DW) 的專用 SQL 集區？

A. 專用的 SQL 集區 (先前的 SQL DW) 指的是 Azure Synapse 正式推出的企業資料倉儲功能。 如需詳細資訊，請參閱 [Azure Synapse Analytics 的內容](sql-data-warehouse-overview-what-is.md)。

Q. 如何? 開始使用 Azure Synapse？

A. 您可以開始使用 [Azure 免費帳戶](https://azure.microsoft.com/free/sql-data-warehouse/) 或 [連絡人銷售以取得詳細資訊](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。

Q. Azure Synapse 為數據安全性提供哪些功能？

A. Azure Synapse 提供數個解決方案來保護資料，例如 TDE 和審核。 如需詳細資訊，請參閱[安全性](sql-data-warehouse-overview-manage-security.md)。

Q. 哪裡可以找到 Azure Synapse 符合規範的法律或商務標準？

A. 請造訪 [Microsoft 合規性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)頁面，依產品取得不同的合規性供應項目，例如 SOC 和 ISO。 首先，依合規性標題選擇。 然後，在頁面右側的 [Microsoft 範圍內雲端服務] 區段中，展開 [Azure]，以查看哪些服務符合 Azure Synapse 規範。

Q. 我可以連接 Power BI 嗎？

A. 可以！ 雖然 Power BI 支援使用 Azure Synapse 的直接查詢，但不適合大量的使用者或即時資料。 若要進一步將 Power BI 效能優化，請考慮在 Azure Analysis Services 或 Analysis Service IaaS 上使用 Power BI。

Q. 哪些專屬的 SQL 集區 (先前的 SQL DW) 容量限制？

A. 請參閱我們目前的[容量限制](sql-data-warehouse-service-capacity-limits.md)頁面。

Q. 為何我的延展/暫停/繼續需要花很久的時間？

A. 有幾個因素會影響計算管理作業的時間。 長時間執行作業的常見案例是交易式回復。 初始化延展或暫停作業時，系統會封鎖所有傳入的工作階段，並清空查詢。 為了使系統能處於穩定的狀態，必須先回復交易才能開始作業。 交易數目越多，或是交易記錄檔的大小越大，將系統還原至穩定的狀態所需的時間便越長，因此使作業停止的時間變得更久。

## <a name="user-support"></a>使用者支援

Q. 我有一個功能要求，應該在哪裡提交？

A. 如果您有功能要求，請在我們的 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 頁面上提交

Q. 我要如何執行某項作業？

A. 如需使用 Azure Synapse 進行開發的協助，您可以在我們的 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) 頁面提出問題。

Q. 我要如何提交支援票證？

A. [支援票證](sql-data-warehouse-get-started-create-support-ticket.md)可以透過 Azure 入口網站提出。

## <a name="sql-languagefeature-support"></a>SQL 語言/功能支援

Q. 支援何種資料類型？

A. 請參閱  [資料類型](sql-data-warehouse-tables-data-types.md)。

Q. 支援哪些資料表功能？

A. 支援許多功能。 在不支援的 [資料表功能](sql-data-warehouse-tables-data-types.md)中，可以找到不支援的功能。

## <a name="tooling-and-administration"></a>工具和系統管理

Q. 專用的 SQL 集區 (先前的 SQL DW) 是否支援 REST Api？

A. 是。 可搭配 SQL Database 的大部分 REST 功能也可搭配專用的 SQL 集區使用， (先前的 SQL DW) 。 您可以在 REST 檔頁面或 [資料庫](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中找到 API 資訊。

## <a name="loading"></a>載入

Q. 支援哪些用戶端驅動程式？

A. 您可以在 [ [連接字串](sql-data-warehouse-connection-strings.md) ] 頁面上找到專用 sql 集區的驅動程式支援 (先前的 sql DW) 

問： PolyBase 支援哪些檔案格式？

答︰Orc、RC、Parquet，以及一般分隔的文字

問：我可以使用 PolyBase 連接到哪些資料來源？

答： [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) 和 [Azure 儲存體 blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

問：連接到 Azure 儲存體 Blob 或 ADLS 時，是否有可能進行計算下推？

答：否，PolyBase 只會與儲存體元件互動。

問︰是否可以連線到 HDI？

答︰HDI 可以使用 ADLS 或 WASB 作為 HDFS 層。 如果您是使用 HDFS 層，則可以將該資料載入專用的 SQL 集區， (先前的 SQL DW) 。 不過，您無法針對 HDI 執行個體產生下推計算。

## <a name="next-steps"></a>後續步驟

如需有關 Azure Synapse 中先前的 SQL DW)  (專用 SQL 集區的詳細資訊，請參閱我們的「 [總覽](sql-data-warehouse-overview-what-is.md) 」頁面。
