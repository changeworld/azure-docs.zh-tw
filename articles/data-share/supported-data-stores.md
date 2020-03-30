---
title: Azure 資料共用中受支援的資料存儲
description: 瞭解支援使用 Azure 資料共用的資料存儲。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501800"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 資料共用中受支援的資料存儲

Azure 資料共用提供開放和靈活的資料共用，包括從不同資料存儲共用和共用的能力。 資料提供程式可以共用來自一種類型的資料存儲的資料，並且其資料消費者可以選擇接收資料到哪個資料存儲。 

在本文中，您將瞭解 Azure 資料共用中支援的豐富的 Azure 資料存儲集。 您還可以找到資料供應商和資料消費者可以利用的資料存儲組合的資訊。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 資料共用中支援哪些資料存儲？ 

下表詳細介紹了 Azure 資料共用的支援資料來源。 

| 資料存放區 | 基於快照的共用 | 就地共用 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |公開預覽 | |
| Azure 突觸分析（以前的 Azure SQL DW） |公開預覽 | |
| Azure 資料總管 | |公開預覽 |

## <a name="data-store-support-matrix"></a>資料存儲支援矩陣

Azure 資料共用在決定使用資料存儲以接受 中的資料時，為數據消費者提供了靈活性。 例如，可以從 Azure SQL 資料庫共用的資料可以接收到 Azure 資料存儲第 2 代、Azure SQL 資料庫或 Azure 突觸分析中。 客戶可以選擇在配置接收的資料共用時接收資料的格式。 

下表詳細介紹了資料消費者在接受和配置其資料共用時的不同組合和選擇。 有關如何配置資料集映射的詳細資訊，請參閱[如何配置資料集映射](how-to-configure-mapping.md)。

|  | Azure Blob 儲存體 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure 突觸分析（以前的 Azure SQL DW） | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>從存儲帳戶共用
Azure 資料共用支援共用來自 Azure 資料湖第 1 代和 Azure 資料湖 Gen2 的檔、資料夾和檔案系統。 它還支援共用 Azure Blob 存儲中的 Blob、資料夾和容器。 當前僅支援塊 Blob。 當資料夾在基於快照的共用中共用時，資料消費者可以選擇創建共用資料的完整副本，或者利用增量快照功能僅複製新檔或更新的檔。 同名的現有檔將被覆蓋。

## <a name="share-from-a-sql-based-source"></a>從基於 SQL 的源共用
Azure 資料共用支援共用來自 Azure SQL 資料庫和 Azure 突觸分析（以前的 Azure SQL DW）的表或視圖。 資料消費者可以選擇將資料作為 csv 或鑲木地板檔接受資料到 Azure 資料湖存儲 Gen2 或 Azure Blob 存儲中。 請注意，預設情況下，檔案格式為 csv。 如果需要，資料消費者可以選擇以鑲木地板格式接收資料。 這可以在接收資料時在資料集映射設置中完成。 

當接受資料到 Azure 資料湖存儲 Gen2 或 Azure Blob 存儲時，完整快照將覆蓋目的檔案的內容。 

資料消費者可以選擇將資料接收到自己選擇的表中。 在這種情況下，如果目標表不存在，Azure 資料共用將創建具有源架構的 SQL 表。 如果目標表已存在同名，則將刪除該表，並覆蓋最新的完整快照。 映射目標表時，可以指定替代架構和表名稱。 當前不支援增量快照。 

從基於 SQL 的源共用具有與防火牆規則和許可權相關的先決條件。 有關詳細資訊，請參閱[共用資料](share-your-data.md)教程的先決條件部分。

## <a name="share-from-azure-data-explorer"></a>從 Azure 資料總管共用
Azure 資料共用支援從 Azure 資料資源管理器群集就地共用資料庫的功能。 資料提供程式可以在資料庫或群集級別共用。 在資料庫級別共用時，資料消費者將只能訪問資料提供程式共用的特定資料庫。 在群集級別共用時，資料消費者可以從提供程式的群集訪問所有資料庫，包括資料提供程式創建的任何將來資料庫。

要訪問共用資料庫，資料消費者需要有自己的 Azure 資料資源管理器群集。 資料消費者的 Azure 資料資源管理器群集需要定位在與資料提供程式的 Azure 資料資源管理器群集相同的 Azure 資料中心中。 建立共用關係時，Azure 資料共用會在提供程式和消費者的 Azure 資料資源管理器群集之間創建符號連結。

Azure 資料資源管理器支援兩種資料引入模式：批次處理和流式處理。 從共用資料庫中的批次處理接收的資料將在資料消費者端出現幾秒鐘到幾分鐘之間。 從流中接收的資料最多可能需要 24 小時才能顯示在資料消費者端。 

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
