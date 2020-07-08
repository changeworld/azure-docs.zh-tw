---
title: 使用 Azure SQL 受控執行個體做為資料庫工作負載目的地的 SSIS 遷移
description: 使用 Azure SQL 受控執行個體做為資料庫工作負載目的地的 SSIS 遷移。
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
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116319"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>使用 Azure SQL 受控執行個體做為資料庫工作負載目的地的 SSIS 遷移

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

將資料庫工作負載從 SQL Server 實例遷移至 Azure SQL 受控執行個體時，您應該熟悉[Azure 資料移轉服務](https://docs.microsoft.com/azure/dms/dms-overview)（DMS），以及[使用 DMS 進行 SQL 受控執行個體遷移的網路拓朴](https://docs.microsoft.com/azure/dms/resource-network-topologies)。

本文著重于遷移儲存在 SSIS 目錄（SSISDB）中的 SQL Server Integration Service （SSIS）套件，以及排程 SSIS 封裝執行的 SQL Server Agent 作業。

## <a name="migrate-ssis-catalog-ssisdb"></a>遷移 SSIS 目錄（SSISDB）

SSISDB 遷移可以使用 DMS 來完成，如將[SSIS 封裝遷移至 SQL 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)一文所述。

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS 作業至 SQL 受控執行個體代理程式

SQL 受控執行個體具有原生、第一級的排程器，就像內部部署 SQL Server Agent。  由於 SSIS 作業的遷移工具尚無法使用，因此必須透過腳本/手動複製，從內部部署 SQL Server Agent 遷移至 SQL 受控執行個體代理程式。

## <a name="additional-resources"></a>其他資源

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [使用 DMS 進行 SQL 受控執行個體遷移的網路拓朴](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [將 SSIS 封裝遷移至 SQL 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>後續步驟

- [連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [執行 Azure 中部署的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
