---
title: Azure Synapse Analytics （先前稱為 SQL DW）常見問題
description: 本文列出來自客戶和開發人員的 Azure Synapse Analytics （先前稱為 SQL DW）的常見問題
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 260086d186f7e2b2d6f76c437057f5fbc8af39b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416079"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics （先前稱為 SQL DW）常見問題

## <a name="general"></a>一般

Q. 什麼是 Azure Synapse？

A. Azure Synapse 是一種無限制的分析服務，可將資料倉儲與海量資料分析整合在一起。 它可讓您自由地使用無伺服器隨選或布建的資源大規模來查詢您的資料。 Azure Synapse 會將這兩個世界結合在一起，透過整合的經驗擷取、準備、管理和處理資料，以滿足立即的 BI 和機器學習需求。 如需詳細資訊，請參閱[什麼是 Azure Synapse 分析](sql-data-warehouse-overview-what-is.md)。

Q. Azure SQL 資料倉儲發生什麼事？

A. Azure Synapse 已演變 Azure SQL 資料倉儲（SQL DW）。 我們已將相同業界領先的資料倉儲帶到全新層級的效能和功能。 您可以使用 Azure Synapse 繼續在生產環境中執行現有的資料倉儲工作負載，並自動受益于目前處於預覽狀態的新功能。 如需詳細資訊，請參閱[什麼是 Azure Synapse 分析](sql-data-warehouse-overview-what-is.md)。

Q. 什麼是 Synapse SQL 集區？

A. Synapse SQL 集區指的是 Azure Synapse 正式提供的企業資料倉儲功能。 如需詳細資訊，請參閱[什麼是 Azure Synapse 分析](sql-data-warehouse-overview-what-is.md)。

Q. 如何? 開始使用 Azure Synapse 嗎？

A. 您可以開始使用[Azure 免費帳戶](https://azure.microsoft.com/free/sql-data-warehouse/)或[連絡人銷售以取得詳細資訊](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。

Q. Azure Synapse 提供的資料安全性功能為何？

A. Azure Synapse 提供數個解決方案來保護資料，例如 TDE 和審核。 如需詳細資訊，請參閱[安全性](sql-data-warehouse-overview-manage-security.md)。

Q. 哪裡可以找到 Azure Synapse 符合的法律或商業標準？

A. 請造訪 [Microsoft 合規性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)頁面，依產品取得不同的合規性供應項目，例如 SOC 和 ISO。
首先，選擇 [依合規性標題]。 然後在頁面右側的 [Microsoft 內部範圍雲端服務] 區段中展開 [Azure]，以查看哪些服務符合 Azure Synapse 規範。

Q. 我可以連接 Power BI 嗎？

A. 可以！ 雖然 Power BI 支援使用 Azure Synapse 進行直接查詢，但它並不適用于大量的使用者或即時資料。 若要進一步優化 Power BI 效能，請考慮在 Azure Analysis Services 或 Analysis Service IaaS 上使用 Power BI。

Q. 什麼是 Synapse SQL 集區容量限制？

A. 請參閱我們目前的[容量限制](sql-data-warehouse-service-capacity-limits.md)頁面。

Q. 為何我的延展/暫停/繼續需要花很久的時間？

A. 有數個因素會影響計算管理作業的時間。 長時間執行作業的常見案例是交易式回復。 初始化延展或暫停作業時，系統會封鎖所有傳入的工作階段，並清空查詢。 為了使系統能處於穩定的狀態，必須先回復交易才能開始作業。 交易數目越多，或是交易記錄檔的大小越大，將系統還原至穩定的狀態所需的時間便越長，因此使作業停止的時間變得更久。

## <a name="user-support"></a>使用者支援

Q. 我有一個功能要求，應該在哪裡提交？

A. 如果您有功能要求，請在我們的 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 頁面上提交

Q. 我要如何執行某項作業？

A. 如需使用 Azure Synapse 進行開發的協助，您可以在我們的[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)頁面上提出問題。

Q. 我要如何提交支援票證？

A. [支援票證](sql-data-warehouse-get-started-create-support-ticket.md)可以透過 Azure 入口網站提出。

## <a name="sql-languagefeature-support"></a>SQL 語言/功能支援

Q. 支援何種資料類型？

A. 請參閱[資料類型](sql-data-warehouse-tables-data-types.md)。

Q. 支援哪些資料表功能？

A. 支援許多功能。 不支援的功能可以在不支援的[資料表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和系統管理

Q. Synapse SQL 集區是否支援 REST Api？

A. 是。 可搭配 SQL Database 使用的大部分 REST 功能也適用于 Synapse SQL 集區。 您可以在 REST 檔頁面或[資料庫](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中找到 API 資訊。

## <a name="loading"></a>載入

Q. 支援哪些用戶端驅動程式？

A. 您可以在 [[連接字串](../sql/connection-strings.md)] 頁面上找到 Synapse SQL 集區的驅動程式支援

問： PolyBase 支援哪些檔案格式？

答︰Orc、RC、Parquet，以及一般分隔的文字

問：我可以使用 PolyBase 連接哪些資料來源？

答： [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md)和[Azure 儲存體 blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

問：連接到 Azure 儲存體 Blob 或 ADLS 時，是否有可能進行計算下推？

答：否，PolyBase 只會與儲存元件互動。

問︰是否可以連線到 HDI？

答︰HDI 可以使用 ADLS 或 WASB 作為 HDFS 層。 如果您使用的是 HDFS 層，您可以將該資料載入 Synapse SQL 集區。 不過，您無法針對 HDI 執行個體產生下推計算。

## <a name="next-steps"></a>後續步驟

如需整個 Azure Synapse 的詳細資訊，請參閱我們的[總覽](sql-data-warehouse-overview-faq.md)頁面。
