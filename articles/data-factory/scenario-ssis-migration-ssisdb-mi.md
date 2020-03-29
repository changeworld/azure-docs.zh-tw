---
title: SSIS 遷移，將 Azure SQL 資料庫託管實例作為資料庫工作負載目標
description: SSIS 遷移，將 Azure SQL 資料庫託管實例作為資料庫工作負載目標。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929786"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS 遷移，將 Azure SQL 資料庫託管實例作為資料庫工作負載目標

將資料庫工作負荷從本地 SQL Server 遷移到 Azure SQL 資料庫託管實例時，應熟悉[Azure 資料移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)（DMS） 以及[使用 DMS 進行的 Azure SQL 資料庫託管實例遷移的網路拓撲](https://docs.microsoft.com/azure/dms/resource-network-topologies)。

本文重點介紹存儲在 SSIS 目錄 （SSISDB） 中的 SQL 伺服器整合服務 （SSIS） 包和計畫執行 SSIS 包的 SQL 伺服器代理作業的遷移。

## <a name="migrate-ssis-catalog-ssisdb"></a>遷移 SSIS 目錄 （SSISDB）

SSISDB 遷移可以使用 DMS 完成，如文章所述：[將 SSIS 包遷移到 Azure SQL 資料庫託管實例](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)。

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS 作業到 Azure SQL 資料庫託管實例代理

Azure SQL 資料庫託管實例具有本機的一流計畫程式，就像本地 SQL Server 代理一樣。  由於 SSIS 作業的遷移工具尚不可用，因此必須通過腳本/手動複製將它們從本地 SQL Server 代理遷移到 Azure SQL 資料庫託管實例代理。

## <a name="additional-resources"></a>其他資源

- [Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [使用 DMS 的 Azure SQL 資料庫託管實例遷移的網路拓撲](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [將 SSIS 套件遷移至 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>後續步驟

- [連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [運行部署在 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
