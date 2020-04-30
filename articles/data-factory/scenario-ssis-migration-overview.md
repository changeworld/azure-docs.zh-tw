---
title: 將內部部署 SSIS 工作負載遷移至 Azure Data Factory 中的 SSIS
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
ms.openlocfilehash: 2b23ffec76de3fa644abe3b65876a60c65c05eb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686011"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>將內部部署 SSIS 工作負載移轉至 ADF 中的 SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>總覽

當您將資料庫工作負載從內部部署 SQL Server 遷移至 Azure 資料庫服務（也就是 Azure SQL Database 或 Azure SQL Database 受控實例）時，SQL Server Integration Services （SSIS）上的 ETL 工作負載也必須遷移。

Azure Data Factory （ADF）中的 Azure SSIS Integration Runtime （IR）支援執行 SSIS 套件。 一旦布建 Azure SSIS IR 之後，您就可以使用熟悉的工具（例如 SQL Server Data Tools （SSDT）/SQL Server Management Studio （SSMS））和命令列公用程式（例如 dtinstall/dtutil/dtexec），在 Azure 中部署和執行您的套件。 如需詳細資訊，請參閱[AZURE SSIS 隨即轉移總覽](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

本文著重于將您的 ETL 工作負載從內部部署 SSIS 遷移至 ADF 中的 SSIS 的過程。 移轉程序由兩個階段組成：**評定**及**移轉**。

## <a name="assessment"></a>評量

若要建立完整的遷移計畫，全面的評估將有助於識別來源 SSIS 套件的問題，而導致無法成功地進行遷移。

Data Migration Assistant (DMA) 是可免費下載的工具，適用於此用途，並可在本機安裝及執行。 您可以建立**Integration Services**類型的 DMA 評估專案，以批次方式評估 SSIS 封裝，並找出下列類別中提供的相容性問題：

- 遷移封鎖程式：這些是會封鎖在 Azure SSIS IR 上執行之遷移來源套件的相容性問題。 DMA 提供指引來協助您解決這些問題。

- 資訊性問題：這些是部分支援或已淘汰的功能，可用於來源套件。 DMA 提供一組完整的建議、Azure 中可用的替代方法，以及減輕解決的步驟。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 封裝的四個儲存類型

- SSIS 目錄（SSISDB）。 這是在 SQL Server 2012 中引進，並包含一組用來處理 SSIS 專案/封裝的預存程式、視圖和資料表值函式。
- 檔案系統。
- SQL Server 系統資料庫（MSDB）。
- SSIS 封裝存放區。 這是位於兩個子類型之上的套件管理層：
  - MSDB，這是 SQL Server 中用來儲存 SSIS 封裝的系統資料庫。
  - 受管理的檔案系統，這是用來儲存 SSIS 套件 SQL Server 安裝路徑中的特定資料夾。

DMA 目前支援從**DMA 版本 v 5.0**開始，儲存在**檔案系統**、**封裝存放區**和**SSIS 目錄**中的封裝批次評估。

取得[DMA](https://docs.microsoft.com/sql/dma/dma-overview)，並[使用它來執行您的套件評估](https://docs.microsoft.com/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>遷移

根據來源 SSIS 封裝的[儲存體類型](#four-storage-types-for-ssis-packages)和資料庫工作負載的遷移目的地 **，遷移 ssis 封裝和**排程 ssis 套件執行**SQL Server Agent 作業**的步驟可能會有所不同。 有兩種案例：

- [做為資料庫工作負載目的地的**Azure SQL Database 受控實例**](#azure-sql-database-managed-instance-as-database-workload-destination)
- [做為資料庫工作負載目的地**Azure SQL Database**](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>做為資料庫工作負載目的地的**Azure SQL Database 受控實例**

| **套件儲存類型** |如何批次處理 SSIS 套件|如何批次處理 SSIS 作業|
|-|-|-|
|SSISDB|[遷移**SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[將 SSIS 作業遷移至 Azure SQL Database 受控實例代理程式](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|檔案系統|透過 dtinstall/dtutil/手動複製，將它們重新部署到檔案共用/Azure 檔案儲存體，或保留在檔案系統中，以透過 VNet/自我裝載 IR 來存取。 如需詳細資訊，請參閱[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li> [在 SSMS 中使用 SSIS 作業遷移嚮導](how-to-migrate-ssis-job-ssms.md)進行遷移 <li>透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server （MSDB）|透過 SSMS/dtutil 將它們匯出至檔案系統/檔案共用/Azure 檔案儲存體。 如需詳細資訊，請參閱[匯出 SSIS 封裝](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|將它們匯出至 [檔案系統]/[檔案共用]/[透過 SSMS/dtutil Azure 檔案儲存體，或透過 dtinstall/dtutil/手動複製將它們重新部署到檔案共用/Azure 檔案儲存體，或將它們保存在檔案系統中，以透過 VNet/自我裝載 IR 來存取。 如需詳細資訊，請參閱 dtutil utility。 如需詳細資訊，請參閱[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

### <a name="azure-sql-database-as-database-workload-destination"></a>做為資料庫工作負載目的地**Azure SQL Database**

| **套件儲存類型** |如何批次處理 SSIS 套件|如何批次處理作業|
|-|-|-|
|SSISDB|透過 SSDT/SSMS 重新部署至 Azure-SSISDB。 如需詳細資訊，請參閱[在 Azure 中部署 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|檔案系統|透過 dtinstall/dtutil/手動複製，將它們重新部署到檔案共用/Azure 檔案儲存體，或保留在檔案系統中，以透過 VNet/自我裝載 IR 來存取。 如需詳細資訊，請參閱[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|<li> [在 SSMS 中使用 SSIS 作業遷移嚮導](how-to-migrate-ssis-job-ssms.md)進行遷移 <li> 透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|SQL Server （MSDB）|透過 SSMS/dtutil 將它們匯出至檔案系統/檔案共用/Azure 檔案儲存體。 如需詳細資訊，請參閱[匯出 SSIS 封裝](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|
|封裝存放區|將它們匯出至 [檔案系統]/[檔案共用]/[透過 SSMS/dtutil Azure 檔案儲存體，或透過 dtinstall/dtutil/手動複製將它們重新部署到檔案共用/Azure 檔案儲存體，或將它們保存在檔案系統中，以透過 VNet/自我裝載 IR 來存取。 如需詳細資訊，請參閱 dtutil utility。 如需詳細資訊，請參閱[dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)。|透過腳本/SSMS/ADF 入口網站，將它們轉換成 ADF 管線/活動/觸發程式。 如需詳細資訊，請參閱[SSMS 排程功能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。|

## <a name="additional-resources"></a>其他資源

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [將 SSIS 工作負載隨即轉移至雲端](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [將 SSIS 套件遷移至 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [將封裝重新部署至 Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>後續步驟

- [驗證部署到 Azure 的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [執行 Azure 中部署的 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [監視 Azure SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [排程 Azure 中的 SSIS 套件執行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
