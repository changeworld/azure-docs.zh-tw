---
title: 整合執行階段
description: 了解 Azure Data Factory 中的整合執行階段。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: b899485589f19a5b74d6d22b4e5dae5fbf3ff604
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827536"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory 中的整合執行階段 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可跨不同網路環境提供下列資料整合功能：

- **資料流程**：在受控 Azure 計算環境中執行 [資料流程](concepts-data-flow-overview.md) 。  
- **資料移動**：在公用網路中的資料存放區和私人網路中的資料存放區之間複製資料 (內部部署或虛擬私人網路) 。 它支援內建的連接器、格式轉換、資料行對應，以及高效能和可調式資料轉送。
- **活動分派**：分派和監視在各種計算服務上執行的轉換活動，例如 Azure Databricks、Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server 等等。
- **SSIS 套件執行**：在受控 Azure 計算環境中，以原生方式執行 SQL Server Integration Services (SSIS) 套件。

在 Data Factory 中，活動可定義要執行的動作。 連結服務可定義目標資料存放區或計算服務。 整合執行階段提供活動與連結服務之間的橋樑。  它是由連結服務或活動所參考，並提供可在其中執行活動或從中分派活動的計算環境。 如此一來，就能在最接近目標資料存放區或計算服務的區域執行活動，效率最高，又滿足安全性和合規性需求。

整合執行時間可以透過 [管理中樞](author-management-hub.md) 以及參考它們的任何活動、資料集或資料流程，在 Azure Data Factory UX 中建立。

## <a name="integration-runtime-types"></a>整合執行階段類型

Data Factory 提供三種類型的 Integration Runtime (IR) ，您應該選擇最適合您所需的資料整合功能和網路環境需求的類型。  這三種類型為：

- Azure
- 自我裝載
- Azure-SSIS

下表描述每個整合執行階段類型的功能和網路支援：

IR 類型 | 公用網路 | 私人網路
------- | -------------- | ---------------
Azure | 資料流程<br/>資料移動<br/>活動分派 | 資料流程<br/>資料移動<br/>活動分派
自我裝載 | 資料移動<br/>活動分派 | 資料移動<br/>活動分派
Azure-SSIS | SSIS 封裝執行 | SSIS 封裝執行


## <a name="azure-integration-runtime"></a>Azure 整合執行階段

Azure 整合執行時間可以：

- 在 Azure 中執行資料流程 
- 在雲端資料存放區之間執行複製活動
- 在公用網路中分派下列轉換活動： Databricks 筆記本/Jar/Python 活動、HDInsight Hive 活動、HDInsight Pig 活動、HDInsight MapReduce 活動、HDInsight Spark 活動、HDInsight 串流活動、Machine Learning 批次執行活動、Machine Learning 更新資源活動、預存程式活動、Data Lake Analytics U SQL 活動、.NET 自訂活動、Web 活動、查閱活動，以及取得中繼資料活動。

### <a name="azure-ir-network-environment"></a>Azure IR 網路環境

Azure Integration Runtime 支援連接到資料存放區，並使用可公開存取的端點來計算服務。 啟用受管理的虛擬網路，Azure Integration Runtime 支援在私人網路環境中使用私人連結服務連接到資料存放區。

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR 計算資源和調整規模
Azure 整合執行階段在 Azure 中提供完全受控、無伺服器的計算。  您不必擔心基礎結構布建、軟體安裝、修補或容量調整。  此外，您只需支付實際使用時間。

Azure 整合執行階段提供原生計算，能夠以安全、可靠且高效能的方式，在雲端資料存放區之間移動資料。  您可以設定要在複製活動上使用的資料整合單位數量，Azure IR 的計算大小會很有彈性地相應增加，您不必明確地調整 Azure Integration Runtime 的大小。 

活動分派是將活動路由傳送至目標計算服務的輕量作業，因此不需要擴大此案例的計算大小。

如需建立和設定 Azure IR 的詳細資訊，請參閱 [如何建立和設定 Azure Integration Runtime](create-azure-integration-runtime.md)。 

> [!NOTE] 
> Azure Integration runtime 具有與資料流程執行時間相關的屬性，可定義用來執行資料流程的基礎計算基礎結構。 

## <a name="self-hosted-integration-runtime"></a>自我裝載整合執行階段

自我裝載 IR 能夠：

- 在雲端資料存放區和私人網路中的資料存放區之間執行複製活動。
- 針對內部部署或 Azure 虛擬網路中的計算資源分派下列轉換活動： HDInsight Hive 活動 (BYOC-攜帶您自己的叢集) 、HDInsight Pig 活動 (BYOC) 、HDInsight MapReduce 活動 (BYOC) 、HDInsight Spark 活動 (BYOC) 、HDInsight 串流活動 (BYOC) 、Machine Learning 批次執行活動、Machine Learning 的資源活動、預存程式活動 Data Lake Analytics (、查閱活動，以及取得中繼資料活動。

> [!NOTE] 
> 使用自我裝載整合執行時間來支援需要自備驅動程式的資料存放區，例如 SAP Hana、MySQL 等等。 如需詳細資訊，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!NOTE] 
> JAVA Runtime Environment (JRE) 是自我裝載 IR 的相依性。 請確認您已在相同的主機上安裝 JRE。

### <a name="self-hosted-ir-network-environment"></a>自我裝載 IR 網路環境

如果您想要安全地在私人網路環境中安全地執行資料整合，而這不會直接從公用雲端環境中執行，您可以在公司防火牆後方或虛擬私人網路內的內部部署環境上安裝自我裝載 IR。  自我裝載整合執行階段對於開放網際網路，只會建立以 HTTP 為基礎的輸出連線。

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>自我裝載 IR 計算資源和調整規模

在內部部署電腦或私人網路內的虛擬機器上安裝自我裝載 IR。 目前，我們只支援在 Windows 作業系統上執行自我裝載 IR。  

若要達到高可用性和延展性，您可以在主動-主動模式下，將邏輯執行個體和多個內部部署機器產生關聯，以相應放大自我裝載 IR。  如需詳細資訊，請參閱操作說明指南中的如何 [建立和設定自我裝載 IR](create-self-hosted-integration-runtime.md) 文章以取得詳細資料。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 整合執行階段

若要隨即轉移現有的 SSIS 工作負載，您可以建立 Azure-SSIS IR，以原生方式執行 SSIS 套件。

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR 網路環境

Azure-SSIS IR 可以佈建在公用網路或私人網路中。  將 Azure-SSIS IR 加入已連線至內部網路的虛擬網路，即可支援內部部署資料存取。  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR 計算資源和調整規模

Azure-SSIS IR 是一個完全受控的 Azure VM 叢集，專門用來執行您的 SSIS 套件。 您可以將自己的 Azure SQL Database 或 SQL 受控執行個體用於 SSIS 專案/套件的目錄 (SSISDB) 。 指定節點大小可以相應增加計算能力，指定叢集的節點數目可以相應放大計算能力。 您可以依需求來停止和啟動 Azure-SSIS 整合執行階段，以掌控其執行成本。

如需詳細資訊，請參閱操作說明指南中的＜如何建立和設定 Azure-SSIS IR＞一文。  建立之後，您可以使用熟悉的工具，例如 SQL Server Data Tools (SSDT) 和 SQL Server Management Studio (SSMS)，就像在內部部署環境中使用 SSIS 一樣，不太需要變更就能部署和管理現有的 SSIS 套件。

如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列文章： 

- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文提供逐步指示，說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 本文將擴充教學課程，並提供使用 SQL 受控執行個體以及將 IR 加入虛擬網路的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會示範如何將更多節點新增至 IR，藉此相應放大 Azure-SSIS IR。 
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure SSIS IR 加入至 Azure 虛擬網路的概念資訊。 它也提供使用 Azure 入口網站來設定虛擬網路，好讓 Azure SSIS IR 可加入虛擬網路的步驟。 

## <a name="integration-runtime-location"></a>整合執行階段位置

### <a name="relationship-between-factory-location-and-ir-location"></a>Factory 位置和 IR 位置之間的關聯性

當客戶建立 data factory 實例時，他們需要指定 data factory 的位置。 Data Factory 位置中儲存資料處理站的中繼資料，也是觸發管道的源頭。 Factory 的中繼資料只會儲存在客戶選擇的區域中，且不會儲存在其他區域中。

同時，資料處理站可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。 此行為會透過[全域可用的 IR](https://azure.microsoft.com/global-infrastructure/services/) 來達成，以確保資料合規性、效率，並降低網路輸出成本。

「IR 位置」定義其後端計算的位置，基本上還會定義執行資料移動、活動分派和 SSIS 套件執行的位置。 IR 位置及其所屬的資料處理站位置可能不同。 

### <a name="azure-ir-location"></a>Azure IR 位置

您可以設定 Azure IR 的特定位置，在此情況下，活動執行或分派將會發生在該特定區域中。

如果您選擇在公用網路中使用自動解析 Azure IR （預設值）

- 針對複製活動，ADF 會盡力自動偵測接收資料存放區的位置，然後在相同區域中使用 IR （如果有的話）或最接近相同地理位置中的 IR;如果無法偵測接收資料存放區的區域，則會使用 data factory 區域中的 IR 作為替代方案。

  例如，您已在美國東部建立您的工廠， 
  
  - 將資料複製到美國西部的 Azure Blob 時，如果 ADF 成功偵測到位於美國西部的 Blob，則會在美國西部的 IR 上執行複製活動;如果區域偵測失敗，則會在美國東部的 IR 上執行複製活動。
  - 將資料複製到無法偵測區域的 Salesforce 時，會在美國東部的 IR 上執行複製活動。

  >[!TIP] 
  >如果您有嚴格的資料合規性需求，且必須確定資料不會離開特定地理位置，您可以明確地在特定區域中建立 Azure IR，並使用 ConnectVia 屬性將連結服務指向此 IR。 例如，如果您想要從英國南部中的 Blob 將資料複製到英國南部中的 Azure Synapse Analytics，而且想要確保資料不會留下 UK，請在英國南部中建立 Azure IR，並將這兩個連結的服務連結至此 IR。

- 針對查閱/GetMetadata/刪除活動執行 (也稱為管線活動) 、轉換活動分派 (也稱為外部活動) ，以及撰寫作業 (測試連線、流覽資料夾清單與資料表清單、預覽資料) 、ADF 在 data factory 區域中使用 IR。

- 針對資料流程，ADF 會在 data factory 區域中使用 IR。 

  > [!TIP] 
  > 最好的作法是確保資料流程在與對應資料存放區相同的區域中執行 (可能的) 。 若要達到此目的，您可以自動解決 Azure IR (如果資料存放區位置與 Data Factory 位置) 相同，或在與資料存放區相同的區域中建立新的 Azure IR 實例，然後在其上執行資料流程。 

如果您啟用受控虛擬網路以進行自動解析 Azure IR，ADF 會在 data factory 區域中使用 IR。 

您可以在 UI 上的管線活動監視檢視中或活動監視承載中，監視在活動執行期間生效的 IR 位置。

### <a name="self-hosted-ir-location"></a>自我裝載 IR 位置

自我裝載 IR 在邏輯上會向 Data Factory 註冊，而用來支援其功能的計算由您提供。 因此，自我裝載 IR 沒有明確的位置屬性。 

用來執行資料移動時，自我裝載 IR 會從來源取出資料，並寫入目的地。

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR 位置

為了在擷取、轉換和下載 (ETL) 工作流程中達到高效能，務必選取正確的 Azure-SSIS IR 位置。

- 您 Azure-SSIS IR 的位置不需要與資料處理站的位置相同，但應該與您自己的 Azure SQL Database 或 SQL 受控執行個體（SSISDB）的位置相同。 如此一來，您的 Azure-SSIS 整合執行階段就可以輕易存取 SSISDB，而不會在不同的位置之間產生過多流量。
- 如果您沒有現有的 SQL Database 或 SQL 受控執行個體，但有內部部署資料來源/目的地，您應該在連線到內部部署網路之虛擬網路的相同位置中，建立新的 Azure SQL Database 或 SQL 受控執行個體。  如此一來，您就可以使用新的 Azure SQL Database 或 SQL 受控執行個體來建立 Azure-SSIS IR，並加入該虛擬網路，全都放在相同的位置中，將不同位置的資料移動降到最低。
- 如果現有 Azure SQL Database 或 SQL 受控執行個體的位置與連線到內部部署網路的虛擬網路位置不同，請先使用現有的 Azure SQL Database 或 SQL 受控執行個體建立您的 Azure-SSIS IR，並將另一個虛擬網路加入相同的位置，然後將虛擬網路設定為不同位置之間的虛擬網路連線。

下圖顯示 Data Factory 及其整合執行階段的位置設定：

![整合執行階段位置](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>決定使用哪一個 IR

### <a name="copy-activity"></a>複製活動

若是複製活動，需要來源和接收連結服務來定義資料流程的方向。 下列邏輯可決定使用哪個整合執行階段執行個體來執行複製： 

- 在**兩個雲端資料來源之間進行複製**：當來源和接收連結服務都使用 Azure IR 時，ADF 會使用區域 Azure IR，如果您選擇自動解析 IR (預設) （如[整合執行時間位置](#integration-runtime-location)一節中所述），則會自動決定 Azure IR 的位置。
- **在雲端資料來源與私人網路中的資料來源之間複製**：如果任一來源或接收連結服務指向自我裝載 IR，則會在自我裝載整合執行階段上執行複製活動。
- **在私人網路中的兩個數據源之間複製**：來源和接收連結服務都必須指向相同的整合執行時間實例，而該整合執行時間會用來執行複製活動。

### <a name="lookup-and-getmetadata-activity"></a>查閱和 GetMetadata 活動

查閱和 GetMetadata 的活動會在與資料存放區連結服務相關聯的整合執行階段上執行。

### <a name="external-transformation-activity"></a>外部轉換活動

使用外部計算引擎的每個外部轉換活動都有一個目標計算連結服務，其指向整合執行時間。 此整合執行時間實例會決定外部手動編碼轉換活動的分派來源位置。

### <a name="data-flow-activity"></a>資料流程活動

資料流程活動會在與其相關聯的 Azure integration runtime 上執行。 資料流程所使用的 Spark 計算取決於您 Azure Integration Runtime 中的資料流程屬性，並由 ADF 完全管理。

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [建立 Azure 整合執行時間](create-azure-integration-runtime.md)
- [建立自我裝載整合執行時間](create-self-hosted-integration-runtime.md)
- [建立 AZURE SSIS 整合運行](create-azure-ssis-integration-runtime.md)時間。 本文將擴充教學課程，並提供使用 SQL 受控執行個體以及將 IR 加入虛擬網路的指示。 
