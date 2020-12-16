---
title: 什麼是 Azure Arc 啟用的于 postgresql 超大規模？
description: 什麼是 Azure Arc 啟用的于 postgresql 超大規模？
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17c0e63b02d61b3aecf70624bc97a468d18f66fe
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607502"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>什麼是 Azure Arc 啟用的于 postgresql 超大規模？

Azure Arc 啟用的于 postgresql 超大規模是 Azure Arc 啟用的資料服務中所提供的其中一個資料庫服務。 無論是在內部部署、邊緣和公用雲端中，Azure Arc 都可讓您在所選基礎結構上使用 Kubernetes 執行 Azure 資料服務。 Azure Arc 啟用的資料服務的價值主張闡述圍繞：
- 永遠保持最新版本
- 彈性擴縮
- 自助服務佈建
- 整合式管理
- 中斷案例支援

如需詳細資訊，請參閱：
- [什麼是已啟用 Azure Arc 的資料服務](overview.md)
- [連線模式和需求](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>比較解決方案

本節說明 Azure Arc 啟用的于 postgresql 超大規模與適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus) 有何不同？

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for 于 postgresql 超大規模 (Citus) ":::

這是 Postgres 資料庫引擎的超大規模形式規格，可作為 Azure 中的「資料庫即服務」 (PaaS) 。 它是由啟用超大規模體驗的 Citus 延伸模組所驅動。 在此外型規格中，服務會在 Microsoft 資料中心內執行，並由 Microsoft 操作。

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 啟用的于 postgresql 超大規模

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 啟用的于 postgresql 超大規模":::

這是 Azure Arc 啟用的資料服務所提供之 Postgres 資料庫引擎的超大規模外型規格。 它也是由啟用超大規模體驗的 Citus 延伸模組所驅動。 在此外型規格中，我們的客戶會提供裝載系統的基礎結構，並加以操作。

## <a name="next-steps"></a>後續步驟
- **建立**
   > **只想要試試看嗎？您沒有可用的 Kubernetes 叢集嗎？我們會為您提供沙箱：**  
   > 在 Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中快速開始使用 [Azure Arc 快速入門](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)。

   - **創建：**
      - [安裝用戶端工具](install-client-tools.md)
      - [建立 Azure Arc 資料控制器](create-data-controller.md) (需要先安裝用戶端工具)
      - [在 Azure Arc (上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組](create-postgresql-hyperscale-server-group.md) 需要先建立 Azure Arc 資料控制器。 ) 
- [**深入瞭解 Azure Arc 啟用的資料服務**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**瞭解 Azure Arc**](https://aka.ms/azurearc)
