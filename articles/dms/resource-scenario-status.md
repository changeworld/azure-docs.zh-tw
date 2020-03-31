---
title: 資料庫移轉案例狀態
titleSuffix: Azure Database Migration Service
description: 瞭解 Azure 資料庫移轉服務支援的遷移方案的狀態。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254922"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure 資料庫移轉服務支援的遷移方案的狀態

Azure 資料庫移轉服務旨在支援離線（一次性）和連線（連續同步）遷移的不同遷移方案（源/目標對）。 Azure 資料庫移轉服務提供的方案覆蓋範圍正在隨著時間的推移而擴展。 定期都會新增新的案例。 本文標識 Azure 資料庫移轉服務當前支援的遷移方案以及每個方案的狀態（專用預覽、公共預覽或通用性）。

## <a name="offline-versus-online-migrations"></a>離線移轉與線上移轉之比較

使用 Azure 資料庫移轉服務，可以執行離線或連線遷移。 若使用「離線」** 移轉，在移轉開始的同時，應用程式也會開始停機。 要將停機時間限制為遷移完成後剪切到新環境所需的時間，請使用*連線*遷移。 建議測試離線遷移以確定停機時間是否可以接受;如果沒有，則進行線上遷移。

## <a name="migration-scenario-status"></a>移轉案例狀態

Azure 資料庫移轉服務支援的遷移方案的狀態隨時間而變化。 通常，方案首先在**私有預覽**中發佈。 參與私人預覽要求客戶通過[DMS 預覽網站](https://aka.ms/dms-preview)提交提名。 私有預覽後，方案狀態將更改為**公共預覽**。 Azure 資料庫移轉服務使用者可以直接從使用者介面在公共預覽中嘗試遷移方案。 無需註冊。  但是，公共預覽版中的遷移方案可能並非在所有區域都可用，並且可能在最終發佈之前進行其他更改。 公共預覽後，方案狀態將更改為**一般可用性**。 常規可用性 （GA） 是最終發佈狀態，並且功能是完整的，並且所有使用者都可以訪問。

## <a name="migration-scenario-support"></a>移轉案例支援

下表顯示了使用 Azure 資料庫移轉服務時支援哪些遷移方案。

> [!NOTE]
> 如果下面列出的方案未顯示在使用者介面中，請與["詢問 Azure 資料庫移轉"](mailto:AskAzureDatabaseMigrations@service.microsoft.com)別名聯繫以獲取其他資訊。

> [!IMPORTANT]
> 要查看 Azure 資料庫移轉服務當前在專用預覽中支援的所有方案，請參閱[DMS 預覽網站](https://aka.ms/dms-preview)。

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
| **Azure 宇宙資料庫** | MongoDB | ✔ | GA |
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
| **Azure 宇宙資料庫** | MongoDB | ✔ | GA |
| **適用於 MySQL 的 Azure DB** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **適用於 PostgreSQL 的 Azure DB** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 公開預覽 |

## <a name="next-steps"></a>後續步驟

有關 Azure 資料庫移轉服務和區域可用性的概述，請參閱"[什麼是 Azure 資料庫移轉服務](dms-overview.md)"一文。
