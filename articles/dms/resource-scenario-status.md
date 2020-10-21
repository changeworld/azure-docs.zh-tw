---
title: 資料庫移轉案例狀態
titleSuffix: Azure Database Migration Service
description: 瞭解 Azure 資料庫移轉服務所支援之遷移案例的狀態。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: cc3323a3ae2d604557d7e09b38252d63f17c5470
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319916"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure 資料庫移轉服務所支援的遷移案例狀態

Azure 資料庫移轉服務是設計用來支援不同的遷移案例 (來源/目標群組) 適用于離線 (一次) 和線上 (持續同步) 遷移。 Azure 資料庫移轉服務所提供的案例涵蓋範圍即將延長一段時間。 定期都會新增新的案例。 本文會識別 Azure 資料庫移轉服務目前支援的遷移案例，以及每個案例的狀態 (個人預覽版、公開預覽或一般可用性) 。

## <a name="offline-versus-online-migrations"></a>離線移轉與線上移轉之比較

透過 Azure 資料庫移轉服務，您可以進行離線或線上遷移。 若使用「離線」** 移轉，在移轉開始的同時，應用程式也會開始停機。 若要將停機時間限制為在遷移完成時切換至新環境所需的時間，請使用 *線上* 遷移。 建議您測試離線遷移，以判斷停機時間是否可接受;如果沒有，請進行線上遷移。

## <a name="migration-scenario-status"></a>移轉案例狀態

Azure 資料庫移轉服務所支援的遷移案例狀態會隨時間而改變。 一般來說，案例會先以 **私人預覽**形式發行。 參與個人預覽版需要客戶透過 [DMS 預覽網站](https://aka.ms/dms-preview)提交提名。 在私人預覽之後，案例狀態會變更為 **公開預覽**。 Azure 資料庫移轉服務使用者可以直接從使用者介面試用公開預覽版中的遷移案例。 不需要註冊。  不過，處於公開預覽狀態的遷移案例可能無法在所有區域中使用，而且可能會在最終發行之前進行額外的變更。 公開預覽之後，案例狀態會變更為 **正式推出**。 正式運作 (GA) 是最終發行狀態，且所有使用者都可以完整且可存取此功能。

## <a name="migration-scenario-support"></a>移轉案例支援

下表顯示使用 Azure 資料庫移轉服務時支援的遷移案例。

> [!NOTE]
> 如果未在使用者介面中顯示如下所列為支援的案例，請洽詢 [要求 Azure 資料庫移轉](mailto:AskAzureDatabaseMigrations@service.microsoft.com) 別名以取得詳細資訊。

> [!IMPORTANT]
> 若要查看 Azure 資料庫移轉服務目前在私人預覽中支援的所有案例，請參閱 [DMS 預覽網站](https://aka.ms/dms-preview)。

### <a name="offline-one-time-migration-support"></a>離線 (單次) 移轉支援

下表顯示適用於離線移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **適用於 MySQL 的 Azure DB** | MySQL | X |   |
|   | RDS MySQL | X |   |
| **適用于于 postgresql 的 Azure DB-單一伺服器** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **適用于于 postgresql 的 Azure DB-超大規模 (Citus) ** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>線上 (持續同步) 移轉支援

下表顯示適用於線上移轉的「Azure 資料庫移轉服務」支援。

| 目標  | 來源 | 支援 | 狀態 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | X |  |
| **Azure SQL VM** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **適用於 MySQL 的 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **適用于于 postgresql 的 Azure DB-單一伺服器** | PostgreSQL | ✔ | GA |
|   | 適用于于 postgresql 的 Azure DB-單一伺服器 | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 公開預覽 |
| **適用于于 postgresql 的 Azure DB-超大規模 (Citus) ** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |


## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的總覽，請參閱「 [什麼是 Azure 資料庫移轉服務](dms-overview.md)」一文。
