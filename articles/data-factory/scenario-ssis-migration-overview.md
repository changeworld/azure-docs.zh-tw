---
title: 在 Azure 資料工廠中將本地 SSIS 工作負載遷移到 SSIS
description: 將本地 SSIS 工作負載遷移到 ADF 中的 SSIS。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929795"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>將本地 SSIS 工作負載遷移到 ADF 中的 SSIS

## <a name="overview"></a>總覽

將資料庫工作負荷從本地 SQL Server 遷移到 Azure 資料庫服務（即 Azure SQL 資料庫或 Azure SQL 資料庫託管實例）時，SQL 伺服器整合服務 （SSIS） 上的 ETL 工作負荷是主要增值之一服務也需要遷移。

Azure 資料工廠 （ADF） 中的 Azure-SSIS 集成運行時 （IR） 支援運行 SSIS 包。 預配 Azure-SSIS IR 後，可以使用熟悉的工具，如 SQL 伺服器資料工具 （SSDT）/SQL 伺服器管理工作室 （SSMS） 和命令列實用程式（如 dtinstall/dtutil/dtexec）在 Azure 中部署和運行包。 有關詳細資訊，請參閱[Azure SSIS 提升和移位概述](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

本文重點介紹 ETL 工作負載從本地 SSIS 到 ADF 中的 SSIS 的遷移過程。 遷移過程由兩個階段組成：**評估和****遷移**。

## <a name="assessment"></a>評量

為了建立完整的遷移計畫，徹底的評估將有助於確定源 SSIS 包的問題，這些問題將阻止遷移成功。

資料移轉助手 （DMA） 是一個可自由下載的工具，可用於此目的，可在本地安裝和執行。 可以創建集成**類型的**DMA 評估專案，以分批評估 SSIS 包，並確定以下類別中的相容性問題：

- 遷移阻止程式：這些是阻止在 Azure-SSIS IR 上運行的遷移源包的相容性問題。 DMA 提供了説明您解決這些問題的指導。

- 資訊性問題：這些是源包中使用的部分支援或棄用功能。 DMA 提供了一組全面的建議、Azure 中可用的替代方法以及需要解決的緩解步驟。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 套裝軟體的四種存儲類型

- SSIS 目錄 （SSISDB）。 SQL Server 2012 介紹了此功能，其中包含一組用於處理 SSIS 專案/包的預存程序、視圖和表值函數。
- 檔案系統。
- SQL 伺服器系統資料庫 （MSDB）。
- SSIS 套裝軟體存儲。 這是兩個子類型之上的包管理層：
  - MSDB，它是 SQL Server 中用於存儲 SSIS 包的系統資料庫。
  - 託管檔案系統，它是用於存儲 SSIS 包的 SQL Server 安裝路徑中的特定資料夾。

DMA 目前支援對存儲在**檔案系統**、**包存儲**和**SSIS 目錄中**的包的批次處理評估，因為**DMA 版本 v5.0**。

獲取[DMA，](https://docs.microsoft.com/sql/dma/dma-overview)並[使用它執行您的包評估](https://docs.microsoft.com/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>移轉

根據源 SSIS 包的[存儲類型](#four-storage-types-for-ssis-packages)和資料庫工作負載的遷移目標，遷移**SSIS 包**和 SQL **Server 代理作業**的步驟可能會有所不同。 有兩種案例：

- [**Azure SQL 資料庫託管實例**作為資料庫工作負載目標](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL 資料庫**作為資料庫工作負載目標](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL 資料庫託管實例**作為資料庫工作負載目標

| **包存儲類型** |如何批量遷移 SSIS 包|如何批量遷移 SSIS 作業|
|-|-|-|
|SSISDB|[遷移**SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[將 SSIS 作業遷移到 Azure SQL 資料庫託管實例代理](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|檔案系統|通過 dtinstall/dtutil/手動複製重新部署它們以檔共用/Azure 檔，或保留檔案系統以通過 VNet/自託管 IR 進行訪問。 有關詳細資訊，請參閱[dtutil 實用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL 伺服器 （MSDB）|通過 SSMS/dtutil 將其匯出到檔案系統/檔共用/Azure 檔。 有關詳細資訊，請參閱匯出[SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|通過 SSMS/dtutil 將其匯出到檔案系統/檔共用/Azure 檔，或通過 dtinstall/dtutil/手動複製將其重新部署到檔共用/Azure 檔，或將它們保存在檔案系統中，以便通過 VNet/自託管 IR 進行訪問。 有關詳細資訊，請參閱 dtil 實用程式。 有關詳細資訊，請參閱[dtutil 實用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL 資料庫**作為資料庫工作負載目標

| **包存儲類型** |如何批量遷移 SSIS 包|如何批次處理遷移作業|
|-|-|-|
|SSISDB|通過 SSDT/SSMS 重新部署到 Azure SSISDB。 有關詳細資訊，請參閱在[Azure 中部署 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|檔案系統|通過 dtinstall/dtutil/手動複製重新部署它們以檔共用/Azure 檔，或保留檔案系統以通過 VNet/自託管 IR 進行訪問。 有關詳細資訊，請參閱[dtutil 實用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL 伺服器 （MSDB）|通過 SSMS/dtutil 將其匯出到檔案系統/檔共用/Azure 檔。 有關詳細資訊，請參閱匯出[SSIS 包](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|通過 SSMS/dtutil 將其匯出到檔案系統/檔共用/Azure 檔，或通過 dtinstall/dtutil/手動複製將其重新部署到檔共用/Azure 檔，或將它們保存在檔案系統中，以便通過 VNet/自託管 IR 進行訪問。 有關詳細資訊，請參閱 dtil 實用程式。 有關詳細資訊，請參閱[dtutil 實用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|通過腳本/SSMS/ADF 門戶將它們轉換為 ADF 管道/活動/觸發器。 有關詳細資訊，請參閱[SSMS 調度功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

## <a name="additional-resources"></a>其他資源

- [Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [提升 SSIS 工作負載並將其轉移到雲中](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [將 SSIS 套件遷移至 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [將包重新部署到 Azure SQL 資料庫](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>後續步驟

- [驗證部署到 Azure 的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [運行部署在 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [監視 Azure-SSIS 集成運行時](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [在 Azure 中計畫 SSIS 包執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
