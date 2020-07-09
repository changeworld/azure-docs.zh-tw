---
title: Azure 資料共用中支援的資料存放區
description: 瞭解支援使用 Azure 資料共用的資料存放區。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 8263e2b2d3d8d029cd28f6c8ea6c556fb9fe23be
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108289"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 資料共用中支援的資料存放區

Azure 資料共用提供開放且彈性的資料共用，包括能夠從和到不同的資料存放區共用。 資料提供者可以共用一種資料存放區的資料，而且其資料取用者可以選擇要接收資料的資料存放區。 

在本文中，您將瞭解 Azure 資料共用中支援的一組豐富 Azure 資料存放區。 您也可以找到資料提供者和資料取用者可利用的資料存放區組合的相關資訊。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 資料共用支援哪些資料存放區？ 

下表詳細說明 Azure 資料共用支援的資料來源。 

| 資料存放區 | 以快照集為基礎的共用 | 就地共用 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |公開預覽 | |
| Azure Synapse 分析（先前稱為 Azure SQL DW） |公開預覽 | |
| Azure 資料總管 | |公開預覽 |

## <a name="data-store-support-matrix"></a>資料存放區支援矩陣

Azure 資料共用可在決定資料存放區以接受中的資料時，為數據取用者提供彈性。 例如，從 Azure SQL Database 共用的資料可以接收到 Azure Data Lake 存放區 Gen2、Azure SQL Database 或 Azure Synapse 分析。 客戶可以在設定接收的資料共用時，選擇要接收資料的格式。 

下表詳細說明資料取用者在接受和設定其資料共用時所擁有的不同組合和選擇。 如需如何設定資料集對應的詳細資訊，請參閱[如何設定資料集](how-to-configure-mapping.md)對應。

| 資料存放區 | Azure Blob 儲存體 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure 資料總管
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse 分析（先前稱為 Azure SQL DW） | ✓ | | ✓ | ✓ | ✓ ||
| Azure 資料總管 |||||| ✓ |

## <a name="share-from-a-storage-account"></a>從儲存體帳戶共用
Azure 資料共用支援從 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共用檔案、資料夾和檔案系統。 它也支援從 Azure Blob 儲存體共用 blob、資料夾和容器。 目前僅支援區塊 blob。 當檔案系統、容器或資料夾在以快照集為基礎的共用中共用時，資料取用者可以選擇建立共用資料的完整複本，或利用增量快照集功能來只複製新的或更新的檔案。 增量快照集會以檔案的上次修改時間為基礎。 將會覆寫具有相同名稱的現有檔案。

## <a name="share-from-a-sql-based-source"></a>從以 SQL 為基礎的來源共用
Azure 資料共用支援從 Azure SQL Database 和 Azure Synapse Analytics （先前稱為 Azure SQL DW）共用資料表或 views。 資料取用者可以選擇將資料接受 Azure Data Lake 存放區 Gen2，或 Azure Blob 儲存體 csv 或 parquet 檔案。 請注意，根據預設，檔案格式為 csv。 資料取用者可以視需要選擇接收 parquet 格式的資料。 這可以在接收資料時，于資料集對應設定中完成。 

將資料接受 Azure Data Lake 存放區 Gen2 或 Azure Blob 儲存體時，完整快照集會覆寫目標檔案的內容。 

資料取用者可以選擇將資料接收到其選擇的資料表。 在此案例中，如果目標資料表不存在，Azure 資料共用會建立具有來源架構的 SQL 資料表。 如果目的地資料表已經存在且具有相同的名稱，它將會被捨棄，並以最新的完整快照集覆寫。 對應目的地資料表時，可以指定替代的架構和資料表名稱。 目前不支援增量快照集。 

從以 SQL 為基礎的來源共用，具有與防火牆規則和許可權相關的先決條件。 如需詳細資訊，請參閱[共用資料](share-your-data.md)教學課程的先決條件一節。

## <a name="share-from-azure-data-explorer"></a>從 Azure 資料總管共用
Azure 資料共用支援從 Azure 資料總管叢集就地共用資料庫的功能。 資料提供者可以在資料庫或叢集層級共用。 當在資料庫層級共用時，資料取用者將只能存取資料提供者所共用的特定資料庫。 在叢集層級共用時，資料取用者可以從提供者的叢集中存取所有資料庫，包括資料提供者所建立的任何未來資料庫。

若要存取共用資料庫，資料取用者必須有自己的 Azure 資料總管叢集。 資料取用者的 Azure 資料總管叢集必須在與資料提供者的 Azure 資料總管叢集相同的 Azure 資料中心內尋找。 建立共用關聯性時，Azure 資料共用會在提供者和取用者的 Azure 資料總管叢集之間建立符號連結。

Azure 資料總管支援兩種資料內嵌模式：批次和串流。 在共用資料庫中從批次收到的資料，將會出現在資料取用者端幾秒鐘到幾分鐘的時間。 從串流接收的資料最多可能需要24小時才會出現在資料取用者端。 

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
