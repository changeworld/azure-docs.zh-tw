---
title: Azure Data Share 中支援的資料存放區
description: 瞭解支援使用 Azure Data Share 的資料存放區。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f3ecf8ef22d3f1d66b7148b809475a830c7e9f13
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318586"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share 中支援的資料存放區

Azure Data Share 提供開放式且彈性的資料共用，包括能夠從不同的資料存放區進行共用。 資料提供者可以共用一種資料存放區的資料，而其資料取用者可以選擇要接收資料的資料存放區。 

在本文中，您將瞭解 Azure Data Share 支援的一組豐富 Azure 資料存放區。 您也可以找到資料存放區組合的資訊，這些資料存放區可由資料提供者和資料取用者利用。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure Data Share 支援哪些資料存放區？ 

下表詳細說明 Azure Data Share 支援的資料來源。 

| 資料存放區 | 以快照集為基礎的共用 | 就地共用 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |✓ | |
| Azure Synapse Analytics (先前的 Azure SQL DW)  |✓ | |
| Azure 資料總管 | |✓ |

## <a name="data-store-support-matrix"></a>資料存放區支援矩陣

當您在決定要接受資料的資料存放區時，Azure Data Share 可提供資料取用者的彈性。 例如，從 Azure SQL Database 共用的資料可以接收 Azure Data Lake 存放區 Gen2、Azure SQL Database 或 Azure Synapse Analytics。 客戶可以在設定接收的資料共用時，選擇接收資料的格式。 

下表詳細說明資料取用者在接受和設定其資料共用時，所擁有的不同組合和選擇。 如需有關如何設定資料集對應的詳細資訊，請參閱 [如何設定資料集](how-to-configure-mapping.md)對應。

| 資料存放區 | Azure Blob 儲存體 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure 資料總管
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (先前的 Azure SQL DW)  | ✓ | | ✓ | ✓ | ✓ ||
| Azure 資料總管 |||||| ✓ |

## <a name="share-from-a-storage-account"></a>從儲存體帳戶共用
Azure Data Share 支援 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共用檔案、資料夾和檔案系統。 它也支援從 Azure Blob 儲存體共用 blob、資料夾和容器。 目前僅支援區塊 blob。 當檔案系統、容器或資料夾在快照集型共用中共用時，資料取用者可以選擇建立共用資料的完整複本，或利用累加式快照集功能只複製新的或更新的檔案。 增量快照集會以檔案的上次修改時間為基礎。 將會覆寫具有相同名稱的現有檔案。

如需詳細資訊，請參閱 [共用和接收 Azure Blob 儲存體的資料，Azure Data Lake Storage](how-to-share-from-storage.md) 。

## <a name="share-from-a-sql-based-source"></a>從以 SQL 為基礎的來源共用
Azure Data Share 支援從 Azure SQL Database 和 Azure Synapse Analytics (先前的 Azure SQL DW) 共用資料表或觀點。 資料取用者可以選擇接受資料 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體 csv 或 parquet 檔案，以及 Azure SQL Database 和 Azure Synapse Analytics 資料表。

將資料接受 Azure Data Lake 存放區 Gen2 或 Azure Blob 儲存體時，完整快照集會覆寫目標檔案的內容（如果已經存在的話）。
當資料收到資料表，且目標資料表不存在時，Azure Data Share 會使用來源架構建立 SQL 資料表。 如果目標資料表已經存在且具有相同名稱，則會將它卸載並以最新的完整快照集加以覆寫。 目前不支援增量快照集。

如需詳細資訊，請參閱 [共用和接收 Azure SQL Database 的資料，Azure Synapse Analytics](how-to-share-from-sql.md) 。

## <a name="share-from-azure-data-explorer"></a>從 Azure 資料總管共用
Azure Data Share 支援從 Azure 資料總管叢集就地共用資料庫的功能。 資料提供者可以在資料庫或叢集層級共用。 在資料庫層級共用時，資料取用者只能存取 (的特定資料庫，) 由資料提供者所共用。 在叢集層級共用時，資料取用者可以從提供者的叢集中存取所有資料庫，包括資料提供者所建立的任何未來資料庫。

若要存取共用資料庫，資料取用者必須有自己的 Azure 資料總管叢集。 資料取用者的 Azure 資料總管叢集必須在與資料提供者的 Azure 資料總管叢集相同的 Azure 資料中心內找到。 建立共用關聯性時，Azure Data Share 會在提供者與取用者的 Azure 資料總管叢集之間建立符號連結。 使用批次模式內嵌至來源 Azure 資料總管叢集中的資料，將會在幾秒鐘內顯示于目標叢集上幾分鐘。

如需詳細資訊，請參閱 [從 Azure 共用和接收資料資料總管](/azure/data-explorer/data-share) 。 

## <a name="next-steps"></a>後續步驟

若要瞭解如何開始共用資料，請繼續進行「 [共用您的資料](share-your-data.md) 」教學課程。
