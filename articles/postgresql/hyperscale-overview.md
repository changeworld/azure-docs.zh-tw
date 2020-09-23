---
title: 適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 概觀
description: 提供超大規模 (Citus) 部署選項概觀
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943827"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>什麼是適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus)？

適用於 PostgreSQL 的 Azure 資料庫是 Microsoft 雲端中專為開發人員建置的關聯式資料庫服務。 此服務是以開放原始碼 [PostgreSQL](https://www.postgresql.org/) 資料庫引擎的社群版本作為基礎。

超大規模 (Citus) 部署選項會使用分區化，水平調整多部電腦上的查詢。 其查詢引擎可平行處理從這些伺服器傳入的 SQL 查詢，以提升大型資料集的回應速度。 相較於其他部署選項，其可為需要更大規模和效能的應用程式提供服務，這些工作負載通常有逼近或超過 100 GB 的資料。

超大規模 (Citus) 可提供：

- 使用分區化的跨多部電腦水平調整
- 跨這些伺服器的查詢平行處理能力，以提升大型資料集的回應速度
- 多租用戶應用程式、即時作業分析和高輸送量交易式工作負載的絕佳支援

針對 PostgreSQL 所建置的應用程式可以在超大規模 (Citus) 上執行分散式查詢，只要有標準[連線程式庫](./concepts-connection-libraries.md)並進行些許變更即可。

## <a name="next-steps"></a>後續步驟

- 從[建立第一個](./quickstart-create-hyperscale-portal.md)適用於 PostgreSQL 的 Azure 資料庫 - 超大規模 (Citus) 伺服器開始。
- 如需成本比較和計算機，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。 超大規模 (Citus) 提供預付的保留執行個體折扣。如需詳細資訊，請參閱[超大規模資料庫 RI 定價](concepts-hyperscale-reserved-pricing.md)頁面。
- 判斷伺服器群組的最佳[初始大小](howto-hyperscale-scaling.md#picking-initial-size)
