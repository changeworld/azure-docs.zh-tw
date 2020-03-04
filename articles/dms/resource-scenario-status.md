---
title: 資料庫移轉案例狀態
titleSuffix: Azure Database Migration Service
description: 瞭解 Azure 資料庫移轉服務所支援的遷移案例狀態。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254922"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure 資料庫移轉服務所支援的遷移案例狀態

Azure 資料庫移轉服務的設計，是為了支援離線（一次性）和線上（連續同步處理）遷移的不同遷移案例（來源/目標群組）。 Azure 資料庫移轉服務所提供的案例涵蓋範圍會隨著時間擴充。 定期都會新增新的案例。 本文會識別 Azure 資料庫移轉服務目前支援的遷移案例，以及每個案例的狀態（私人預覽、公開預覽或一般可用性）。

## <a name="offline-versus-online-migrations"></a>離線移轉與線上移轉之比較

透過 Azure 資料庫移轉服務，您可以進行離線或線上遷移。 若使用「離線」移轉，在移轉開始的同時，應用程式也會開始停機。 若要在遷移完成時，將停機時間限制為切換到新環境所需的時間，請使用*線上*遷移。 建議您測試離線遷移，以判斷停機時間是否可接受;如果沒有，請進行線上遷移。

## <a name="migration-scenario-status"></a>移轉案例狀態

Azure 資料庫移轉服務所支援的遷移案例狀態會隨著時間而不同。 一般來說，案例會先以**私人預覽**的形式發行。 參與個人預覽版需要客戶透過[DMS 預覽網站](https://aka.ms/dms-preview)提交提名。 在私人預覽之後，案例狀態會變更為 [**公開預覽**]。 Azure 資料庫移轉服務使用者可以直接從使用者介面試用公開預覽中的遷移案例。 不需要註冊。  不過，公開預覽中的遷移案例可能無法在所有區域使用，而且可能會在最終版本之前進行其他變更。 公開預覽之後，案例狀態會變更為 [**正式推出**]。 公開上市（GA）是最終的發行狀態，而且功能已完整且可供所有使用者存取。

## <a name="migration-scenario-support"></a>移轉案例支援

下表顯示使用 Azure 資料庫移轉服務時支援的遷移案例。

> [!NOTE]
> 如果以下列出的案例不會出現在使用者介面中，請洽詢[詢問 Azure 資料庫移轉](mailto:AskAzureDatabaseMigrations@service.microsoft.com)別名以取得其他資訊。

> [!IMPORTANT]
> 若要在私人預覽中查看 Azure 資料庫移轉服務目前支援的所有案例，請參閱[DMS preview 網站](https://aka.ms/dms-preview)。

### <a name="offline-one-time-migration-support"></a>離線 (單次) 移轉支援

下表顯示適用於離線移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **適用於 MySQL 的 Azure DB** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **適用於 PostgreSQL 的 Azure DB** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>線上 (持續同步) 移轉支援

下表顯示適用於線上移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 個人預覽版 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **適用於 MySQL 的 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **適用於 PostgreSQL 的 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 公開預覽 |

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的總覽，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
