---
title: '將內部部署 SQL Server Integration Services (SSIS) 工作負載遷移至 Azure Data Factory (ADF 中的 SSIS) '
description: 將內部部署 SSIS 工作負載遷移至 ADF 中的 SSIS。
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
ms.openlocfilehash: c2b95108b8c6b1e4db9d5a494e64774609ed5574
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322643"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>將內部部署 SSIS 工作負載移轉至 ADF 中的 SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概觀

當您將資料庫工作負載從內部部署 SQL Server 遷移至 Azure 資料庫服務時（也就是 Azure SQL Database 或 Azure SQL 受控執行個體），SQL Server Integration Services (SSIS 上的 ETL 工作負載) 作為其中一個主要值新增的服務也必須遷移。

Azure Data Factory (ADF) 中的 Azure-SSIS Integration Runtime (IR) 支援執行 SSIS 套件。 布建 Azure-SSIS IR 之後，您就可以使用熟悉的工具，例如 SQL Server Data Tools (SSDT) /SQL Server Management Studio (SSMS) ，以及命令列公用程式（例如 dtinstall/dtutil/dtexec），在 Azure 中部署和執行您的套件。 如需詳細資訊，請參閱 [AZURE SSIS 隨即轉移總覽](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

本文將重點放在 ADF 中從內部部署 SSIS 到 SSIS 的 ETL 工作負載的遷移程式。 移轉程序由兩個階段組成：**評定**及**移轉**。

## <a name="assessment"></a>評量

若要建立完整的遷移計畫，全面的評量將有助於識別來源 SSIS 套件的問題，這會導致無法成功的遷移。

Data Migration Assistant (DMA) 是可免費下載的工具，適用於此用途，並可在本機安裝及執行。 您可以建立 **Integration Services** 類型的 DMA 評定專案，以批次方式評估 SSIS 套件，並找出下列類別中提供的相容性問題：

- 遷移封鎖程式：封鎖遷移來源封裝在 Azure-SSIS IR 上執行的相容性問題。 DMA 提供指引來協助您解決這些問題。

- 資訊性問題：在來源套件中使用部分支援或已淘汰的功能。 DMA 提供一組完整的建議、Azure 中可用的替代方法，以及可減輕的解決步驟。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 套件的四種儲存體類型

-  (SSISDB) 的 SSIS 目錄。 在 SQL Server 2012 中引進，並包含一組用來處理 SSIS 專案/套件的預存程式、視圖和資料表值函數。
- 檔案系統。
- SQL Server 系統資料庫 (MSDB) 。
- SSIS 封裝存放區。 封裝管理層位於兩個子類型之上：
  - MSDB，也就是 SQL Server 中用來儲存 SSIS 封裝的系統資料庫。
  - 受控檔案系統，這是用來儲存 SSIS 套件 SQL Server 安裝路徑中的特定資料夾。

DMA 目前支援自**dma 5.0 版**起，儲存在**檔案系統**、**封裝存放區**和**SSIS 目錄**中的封裝批次評估。

取得 [DMA](https://docs.microsoft.com/sql/dma/dma-overview)，並 [使用它執行您的套件評](https://docs.microsoft.com/sql/dma/dma-assess-ssis)量。

## <a name="migration"></a>移轉

根據來源 SSIS 封裝的 [儲存體類型](#four-storage-types-for-ssis-packages) 和資料庫工作負載的遷移目的地，遷移  **ssis** 封裝和執行排程 ssis 套件執行 **SQL Server Agent 作業** 的步驟可能會有所不同。 有兩種案例：

- [作為資料庫工作負載目的地的**AZURE SQL 受控執行個體**](#azure-sql-managed-instance-as-database-workload-destination)
- [作為資料庫工作負載目的地的**Azure SQL Database**](#azure-sql-database-as-database-workload-destination)

它也是使用 [SSIS DevOps 工具](https://docs.microsoft.com/sql/integration-services/devops/ssis-devops-overview)的實際方式，可將 batch 套件重新部署至遷移目的地。  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>作為資料庫工作負載目的地的**AZURE SQL 受控執行個體**

| **封裝儲存體類型** |如何批次處理 SSIS 套件|如何批次處理 SSIS 作業|
|-|-|-|
|SSISDB|[遷移 **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[將 SSIS 作業遷移至 Azure SQL 受控執行個體代理程式](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|檔案系統|透過 dtinstall/dtutil/手動複製將它們重新部署至檔案共用/Azure 檔案儲存體，或保留在檔案系統中，以透過 VNet/自我裝載 IR 進行存取。 如需詳細資訊，請參閱 [dtutil 公用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li>[將 SSIS 作業遷移至 Azure SQL 受控執行個體代理程式](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> 使用[SSMS 中的 SSIS 作業遷移 Wizard](how-to-migrate-ssis-job-ssms.md)進行遷移 <li>透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server (MSDB) |透過 SSMS/dtutil 將它們匯出到檔案系統/檔案共用/Azure 檔案儲存體。 如需詳細資訊，請參閱 [匯出 SSIS 封裝](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|透過 SSMS/dtutil 將它們匯出到套件存放區，或透過 dtinstall/dtutil/手動複製將它們重新部署至封裝存放區。 如需詳細資訊，請參閱 [使用 Azure-SSIS Integration Runtime 套件存放區管理套件](azure-ssis-integration-runtime-package-store.md)。|<li>[將 SSIS 作業遷移至 Azure SQL 受控執行個體代理程式](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> 透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

### <a name="azure-sql-database-as-database-workload-destination"></a>作為資料庫工作負載目的地的**Azure SQL Database**

| **封裝儲存體類型** |如何批次處理 SSIS 套件|如何批次處理作業|
|-|-|-|
|SSISDB|透過 SSDT/SSMS 重新部署至 Azure-SSISDB。 如需詳細資訊，請參閱 [在 Azure 中部署 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|檔案系統|透過 dtinstall/dtutil/手動複製將它們重新部署至檔案共用/Azure 檔案儲存體，或保留在檔案系統中，以透過 VNet/自我裝載 IR 進行存取。 如需詳細資訊，請參閱 [dtutil 公用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li> 使用[SSMS 中的 SSIS 作業遷移 Wizard](how-to-migrate-ssis-job-ssms.md)進行遷移 <li> 透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server (MSDB) |透過 SSMS/dtutil 將它們匯出到檔案系統/檔案共用/Azure 檔案儲存體。 如需詳細資訊，請參閱 [匯出 SSIS 封裝](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|透過 SSMS/dtutil 將它們匯出到檔案系統/檔案共用/Azure 檔案儲存體，或透過 dtinstall/dtutil/手動複製將它們重新部署至檔案共用/Azure 檔案儲存體，或將它們保存在檔案系統中，以透過 VNet/自我裝載 IR 進行存取。 如需詳細資訊，請參閱 dtutil 公用程式。 如需詳細資訊，請參閱 [dtutil 公用程式](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱 [SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

## <a name="additional-resources"></a>其他資源

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [資料庫移轉小幫手](https://docs.microsoft.com/sql/dma/dma-overview)
- [將 SSIS 工作負載隨即轉移至雲端](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps 工具](https://docs.microsoft.com/sql/integration-services/devops/ssis-devops-overview)
- [將 SSIS 套件遷移至 Azure SQL 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [將套件重新部署到 Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

- [Azure-SSIS Integration Runtime 的內部部署資料存取](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [自訂 Azure-SSIS Integration Runtime 的安裝](how-to-configure-azure-ssis-ir-custom-setup.md)
- [在 Azure 中從 SSIS 套件使用 Windows 驗證來存取資料存放區和檔案共用](ssis-azure-connect-with-windows-auth.md)
- [使用受控識別驗證](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [使用 Azure 金鑰保存庫](store-credentials-in-key-vault.md)
- [設定 Azure-SSIS Integration Runtime 以獲得高效能](configure-azure-ssis-integration-runtime-performance.md)
- [如何按照排程來啟動和停止 Azure-SSIS Integration Runtime](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>後續步驟

- [驗證部署到 Azure 的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [執行部署在 Azure 中的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [監視 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [排程 Azure 中的 SSIS 套件執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
