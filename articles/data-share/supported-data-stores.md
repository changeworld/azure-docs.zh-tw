---
title: Azure Data Share 中支援的資料存放區
description: 瞭解 Azure Data Share 中支援使用的資料存放區。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963674"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share 中支援的資料存放區

Azure Data Share 提供開放式且彈性的資料共用，包括能夠從不同的資料存放區進行共用。 資料提供者可以共用一種資料存放區的資料，而資料取用者可以選擇要接收資料的資料存放區。 

在本文中，您將瞭解 Azure Data Share 支援的一組豐富 Azure 資料存放區。 您也將瞭解資料提供者和資料取用者如何合併不同的資料存放區。 

## <a name="supported-data-stores"></a>支援的資料存放區 

下表說明 Azure Data Share 支援的資料存放區。 

| 資料存放區 | 以完整快照集為基礎的共用 | 以增量快照集為基礎的共用 | 就地共用 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 儲存體 |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (先前稱為 Azure SQL 資料倉儲) |✓ | | |
| Azure Synapse Analytics (工作區) 專用的 SQL 集區 |✓ | | |
| Azure 資料總管 | | |✓ |

## <a name="data-store-support-matrix"></a>資料存放區支援矩陣

Azure Data Share 可讓資料取用者選擇資料存放區來接受資料。 例如，您可以在 Azure Data Lake Storage Gen2、Azure SQL Database 或 Azure Synapse Analytics 中收到 Azure SQL Database 共用的資料。 當客戶設定接收資料共用時，他們可以選擇接收資料的格式。 

下表說明資料取用者接受和設定資料共用時，可選擇的組合和選項。 如需詳細資訊，請參閱 [設定資料集對應](how-to-configure-mapping.md)。

| 資料存放區 | Blob 儲存體 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (先前的 SQL 資料倉儲)  | Synapse Analytics (workspace) 專用的 SQL 集區 | 資料總管
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob 儲存體 | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (先前的 SQL 資料倉儲)  | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (workspace) 專用的 SQL 集區 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| 資料總管 ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>從儲存體帳戶共用
Azure Data Share 支援從 Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 共用檔案、資料夾和檔案系統。 它也支援從 Azure Blob 儲存體共用 blob、資料夾和容器。 目前僅支援區塊 blob。 

當檔案系統、容器或資料夾在以快照集為基礎的共用中共用時，資料取用者可以選擇建立共用資料的完整複本。 或者，他們可以使用累加式快照集功能只複製新檔案或更新檔案。 

增量快照集會以檔案的上次修改時間為基礎。 快照中會覆寫與所接收資料中的檔案同名的現有檔案。 未在目標上刪除從來源刪除的檔案。 

如需詳細資訊，請參閱 [共用和接收來自 Azure Blob 儲存體和 Azure Data Lake Storage 的資料](how-to-share-from-storage.md)。

## <a name="share-from-a-sql-based-source"></a>從 SQL 型來源共用
Azure Data Share 支援從 Azure SQL Database 和 Azure Synapse Analytics (先前的 Azure SQL 資料倉儲) 共用資料表和觀點。 它支援將資料表從 Azure Synapse Analytics 的 (工作區共用) 專用的 SQL 集區。 目前不支援從 Azure Synapse Analytics 的 (工作區共用) 無伺服器 SQL 集區。 

資料取用者可以選擇接受資料 Azure Data Lake Storage Gen2，或 Azure Blob 儲存體為 CSV 檔案或 parquet 檔案。 他們也可以將資料當作資料表接受 Azure SQL Database 和 Azure Synapse Analytics。

當取用者接受資料進入 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體時，如果檔案已存在，則完整快照集會覆寫目標檔案的內容。 當資料收到資料表，但目標資料表尚不存在時，Azure Data Share 會使用來源架構來建立 SQL 資料表。 如果目標資料表已經存在，而且具有相同名稱，則會將它卸載並以最新的完整快照集加以覆寫。 目前不支援增量快照集。

如需詳細資訊，請參閱 [共用和接收來自 Azure SQL Database 和 Azure Synapse Analytics 的資料](how-to-share-from-sql.md)。

## <a name="share-from-data-explorer"></a>從資料總管共用
Azure Data Share 支援從 Azure 資料總管叢集就地共用資料庫的功能。 資料提供者可以在資料庫或叢集層級共用。 

當資料在資料庫層級共用時，資料取用者只能存取資料提供者所共用的資料庫。 當提供者在叢集層級共用資料時，資料取用者可以從提供者的叢集中存取所有資料庫，包括資料提供者所建立的任何未來資料庫。

若要存取共用資料庫，資料取用者需要自己的 Azure 資料總管叢集。 其叢集必須與資料提供者的 Azure 資料總管叢集位於相同的 Azure 資料中心。 

建立共用關聯性時，Azure Data Share 會在提供者的叢集與取用者的叢集之間建立符號連結。 使用批次模式內嵌至來源叢集中的資料，會在幾分鐘內出現在目標叢集上。

如需詳細資訊，請參閱 [從 Azure 共用和接收資料資料總管](/azure/data-explorer/data-share)。 

## <a name="next-steps"></a>後續步驟

若要瞭解如何開始共用資料，請繼續進行「 [共用您的資料](share-your-data.md) 」教學課程。
