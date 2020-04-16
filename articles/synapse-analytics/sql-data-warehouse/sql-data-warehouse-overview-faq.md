---
title: Azure 突觸分析(以前為 SQL DW)常見問題
description: 本文列出了客戶和開發人員提出的有關 Azure 突觸分析(以前為 SQL DW)的常見問題
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416079"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure 突觸分析(以前為 SQL DW) 常見問題

## <a name="general"></a>一般

Q. 什麼是 Azure Synapse？

A. Azure Synapse 是一種無限的分析服務,將數據倉庫和大數據分析結合在一起。 它使您能夠自由地按術語查詢數據,使用無伺服器按需資源或預配資源 - 規模。 Azure Synapse 將這兩個世界與統一體驗相結合,可進行攝用、準備、管理和服務數據,以滿足即時 BI 和機器學習需求。 有關詳細資訊,請參閱[什麼是 Azure 突觸分析](sql-data-warehouse-overview-what-is.md)。

Q. Azure SQL 資料倉庫發生了什麼情況?

A. Azure 突觸是 Azure SQL 資料倉庫 (SQL DW) 演變而來的。 我們將同行業領先的數據倉庫帶到了全新的性能和功能級別。 您可以使用 Azure Synapss 繼續在生產中運行現有數據倉庫工作負荷,並自動受益於預覽版中的新功能。 有關詳細資訊,請參閱什麼是[Azure 突觸分析](sql-data-warehouse-overview-what-is.md)。

Q. 什麼是 Synapse SQL 池?

A. 突觸 SQL 池是指 Azure Synaps 通常可用的企業數據倉庫功能。 有關詳細資訊,請參閱[什麼是 Azure 突觸分析](sql-data-warehouse-overview-what-is.md)。

Q. 如何開始使用 Azure 突觸?

A. 您可以開始使用 Azure[免費帳戶](https://azure.microsoft.com/free/sql-data-warehouse/)或[聯絡銷售人員瞭解更多資訊](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。

Q. Azure Synapse 為數據安全提供了哪些功能?

A. Azure Synapse 提供了多種用於保護數據(如 TDE 和審核)的解決方案。 如需詳細資訊，請參閱[安全性](sql-data-warehouse-overview-manage-security.md)。

Q. 在哪裡可以找到 Azure Synapse 符合哪些法律或業務標準?

A. 請造訪 [Microsoft 合規性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)頁面，依產品取得不同的合規性供應項目，例如 SOC 和 ISO。
首先,按合規標題進行選擇。 然後在頁面右側的 Microsoft 範圍雲服務部分中展開 Azure,以查看哪些服務符合 Azure Synapse 要求。

Q. 我可以連接電源 BI 嗎?

A. 可以！ 儘管 Power BI 支援使用 Azure 突觸直接查詢,但它不適用於大量使用者或即時數據。 要進一步優化 Power BI 性能,請考慮在 Azure 分析服務或分析服務 IaaS 之上使用 Power BI。

Q. 什麼是 Synapse SQL 池容量限制?

A. 請參閱我們目前的[容量限制](sql-data-warehouse-service-capacity-limits.md)頁面。

Q. 為何我的延展/暫停/繼續需要花很久的時間？

A. 有幾個因素會影響計算管理操作的時間。 長時間運行操作的常見情況是事務回滾。 初始化延展或暫停作業時，系統會封鎖所有傳入的工作階段，並清空查詢。 為了使系統能處於穩定的狀態，必須先回復交易才能開始作業。 交易數目越多，或是交易記錄檔的大小越大，將系統還原至穩定的狀態所需的時間便越長，因此使作業停止的時間變得更久。

## <a name="user-support"></a>使用者支援

Q. 我有一個功能要求，應該在哪裡提交？

A. 如果您有功能要求，請在我們的 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 頁面上提交

Q. 我要如何執行某項作業？

A. 有關使用 Azure 突觸進行開發的説明,您可以在[我們的堆疊溢出](https://stackoverflow.com/questions/tagged/azure-sqldw)頁上提問。

Q. 我要如何提交支援票證？

A. [支援票證](sql-data-warehouse-get-started-create-support-ticket.md)可以透過 Azure 入口網站提出。

## <a name="sql-languagefeature-support"></a>SQL 語言/功能支援

Q. 支援何種資料類型？

A. 請參考[資料型態](sql-data-warehouse-tables-data-types.md)。

Q. 支援哪些資料表功能？

A. 支援許多功能。 不支援的功能可以在[不支援的表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和系統管理

Q. Synapse SQL 池是否支援 REST API?

A. 是。 大多數可用於 SQL 資料庫的 REST 功能也可用於 Synapse SQL 池。 您可以在 REST 文件頁或[資料庫中](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)找到 API 資訊。

## <a name="loading"></a>載入

Q. 支援哪些用戶端驅動程式？

A. 在[連接字串](../sql/connection-strings.md)頁面上找到對 Synapse SQL 池的驅動程式支援

問:PolyBase 支援哪些檔案格式?

答︰Orc、RC、Parquet，以及一般分隔的文字

問:可以使用 PolyBase 連接到哪些數據來源?

答[:Azure 資料湖儲存](sql-data-warehouse-load-from-azure-data-lake-store.md)與[Azure 儲存 Blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

問:連接到 Azure 儲存 Blob 或 ADLS 時,計算是否可以向下?

答:否,PolyBase 僅與存儲元件交互。

問︰是否可以連線到 HDI？

答︰HDI 可以使用 ADLS 或 WASB 作為 HDFS 層。 如果具有 HDFS 層,則可以將數據載入到 Synapse SQL 池中。 不過，您無法針對 HDI 執行個體產生下推計算。

## <a name="next-steps"></a>後續步驟

有關 Azure Synapss 作為一個整體的詳細資訊,請參閱我們的[概述](sql-data-warehouse-overview-faq.md)頁面。
