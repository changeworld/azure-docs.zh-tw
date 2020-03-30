---
title: 整合執行階段
description: 了解 Azure Data Factory 中的整合執行階段。
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: 4077e1e00b606480ec93feacbad3c841c0de1ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336186"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory 中的整合執行階段
整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可跨不同網路環境提供下列資料整合功能：

- **資料流程**：在託管 Azure 計算環境中執行[資料流程](concepts-data-flow-overview.md)。  
- **資料移動**：在公共網路中的資料存儲和私人網路絡（本地或虛擬私人網路絡）中的資料存儲中複製資料。 它支援內建的連接器、格式轉換、資料行對應，以及高效能和可調式資料轉送。
- **活動調度**：調度和監視在各種計算服務上運行的轉換活動，如 Azure 資料塊、Azure HDInsight、Azure 機器學習、Azure SQL 資料庫、SQL Server 等。
- **SSIS 套件執行**：在受控 Azure 計算環境中，以原生方式執行 SQL Server Integration Services (SSIS) 套件。

在 Data Factory 中，活動可定義要執行的動作。 連結服務可定義目標資料存放區或計算服務。 整合執行階段提供活動與連結服務之間的橋樑。  它由連結的服務或活動引用，並提供運行活動或從其中調度的活動的計算環境。 如此一來，就能在最接近目標資料存放區或計算服務的區域執行活動，效率最高，又滿足安全性和合規性需求。

## <a name="integration-runtime-types"></a>整合執行階段類型
Data Factory 提供三種整合執行階段，您應該選擇最符合所需之資料整合功能和網路環境需求的類型。  這三種類型為：

- Azure
- 自我裝載
- Azure-SSIS

下表描述每個整合執行階段類型的功能和網路支援：

IR 類型 | 公用網路 | 私人網路
------- | -------------- | ---------------
Azure | 資料流程<br/>資料移動<br/>活動分派 | &nbsp;
自我裝載 | 資料移動<br/>活動分派 | 資料移動<br/>活動分派
Azure-SSIS | SSIS 封裝執行 | SSIS 封裝執行

下圖顯示如何結合使用不同的整合執行階段，以提供豐富的資料整合功能和網路支援：

![不同類型的整合執行階段](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure 整合執行階段
Azure 整合執行階段能夠：

- 在 Azure 中運行資料流程 
- 在雲端資料存放區之間執行複製活動
- 在公共網路中調度以下轉換活動：Databricks 筆記本/Jar/Python 活動、HDInsight Hive 活動、HDInsight Pig 活動、HDInsight MapReduce 活動、HDInsight Spark 活動、HDInsight 流活動、機器學習批次處理執行活動、機器學習更新資源活動、預存程序活動、資料湖分析 U-SQL 活動、.NET 自訂活動、Web 活動、查找活動和獲取中繼資料活動。

### <a name="azure-ir-network-environment"></a>Azure IR 網路環境
Azure 集成運行時支援使用公共可訪問終結點連接到資料存儲和計算服務。 在 Azure 虛擬網路環境中使用自我裝載整合執行階段。

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR 計算資源和調整規模
Azure 整合執行階段在 Azure 中提供完全受控、無伺服器的計算。  您不必擔心基礎結構配置、軟體安裝、修補或容量擴展。  此外，您只需支付實際使用時間。

Azure 整合執行階段提供原生計算，能夠以安全、可靠且高效能的方式，在雲端資料存放區之間移動資料。  您可以設定要在複製活動上使用的資料整合單位數量，Azure IR 的計算大小會很有彈性地相應增加，您不必明確地調整 Azure Integration Runtime 的大小。 

活動調度是將活動路由到目標計算服務的羽量級操作，因此無需為此方案擴展計算大小。

如需有關建立及設定 Azure IR 的資訊，請參閱操作說明指南中的＜如何建立和設定 Azure IR＞。 

> [!NOTE] 
> Azure 集成運行時具有與資料流程運行時相關的屬性，該流定義將用於在上運行資料流程的基礎計算基礎結構。 

## <a name="self-hosted-integration-runtime"></a>自我裝載整合執行階段
自我裝載 IR 能夠：

- 在雲端資料存放區和私人網路中的資料存放區之間執行複製活動。
- 針對本地或 Azure 虛擬網路中的計算資源調度以下轉換活動：HDInsight Hive 活動（BYOC-自帶群集）、HDInsight 豬活動 （BYOC）、HDInsight Map 減少活動 （BYOC）、HDInsight 火花活動 （BYOC）、HDInsight 流活動 （BYOC）、機器學習批次處理執行活動、機器學習更新資源活動、預存程序活動、資料湖分析 U-SQL 活動、自訂活動（在 Azure 批次處理上運行）、查找活動，獲取中繼資料活動。

> [!NOTE] 
> 使用自託管集成運行時支援需要自帶驅動程式（如 SAP Hana、MySQL 等）的資料存儲。 有關詳細資訊，請參閱[受支援的資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!NOTE] 
> JAVA 運行時環境 （JRE） 是自託管 IR 的依賴項。 請確保在同一主機上安裝了 JRE。

### <a name="self-hosted-ir-network-environment"></a>自我裝載 IR 網路環境
如果想要在私人網路環境 (無法從公用雲端環境直接存取) 中安全地執行資料整合，您可以在內部部署環境、公司防火牆後方或虛擬私人網路內安裝自我裝載 IR。  自我裝載整合執行階段對於開放網際網路，只會建立以 HTTP 為基礎的輸出連線。

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>自我裝載 IR 計算資源和調整規模
自我裝載 IR 必須安裝在私人網路內的內部部署機器或虛擬機器上。 目前，我們只支援在 Windows 作業系統上執行自我裝載 IR。  

若要達到高可用性和延展性，您可以在主動-主動模式下，將邏輯執行個體和多個內部部署機器產生關聯，以相應放大自我裝載 IR。  有關詳細資訊，請參閱如何在如何指導詳細資訊下[創建和配置自承載的 IR](create-self-hosted-integration-runtime.md)文章。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 整合執行階段
若要隨即轉移現有的 SSIS 工作負載，您可以建立 Azure-SSIS IR，以原生方式執行 SSIS 套件。

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR 網路環境
Azure-SSIS IR 可以佈建在公用網路或私人網路中。  將 Azure-SSIS IR 加入已連線至內部網路的虛擬網路，即可支援內部部署資料存取。  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR 計算資源和調整規模
Azure-SSIS IR 是一個完全受控的 Azure VM 叢集，專門用來執行您的 SSIS 套件。 您可以自備 Azure SQL Database 或受控執行個體伺服器，以裝載要附加至伺服器的 SSIS 專案/套件 (SSISDB) 目錄。 指定節點大小可以相應增加計算能力，指定叢集的節點數目可以相應放大計算能力。 您可以依需求來停止和啟動 Azure-SSIS 整合執行階段，以掌控其執行成本。

如需詳細資訊，請參閱操作說明指南中的＜如何建立和設定 Azure-SSIS IR＞一文。  建立之後，您可以使用熟悉的工具，例如 SQL Server Data Tools (SSDT) 和 SQL Server Management Studio (SSMS)，就像在內部部署環境中使用 SSIS 一樣，不太需要變更就能部署和管理現有的 SSIS 套件。

如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列文章： 

- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文提供創建 Azure-SSIS IR 的分步說明，並使用 Azure SQL 資料庫託管 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 這篇文章會詳述教學課程，並提供使用 Azure SQL Database 受控執行個體，以及將 IR 加入虛擬網路的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會示範如何將更多節點新增至 IR，藉此相應放大 Azure-SSIS IR。 
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure SSIS IR 加入至 Azure 虛擬網路的概念資訊。 它也提供使用 Azure 入口網站來設定虛擬網路，好讓 Azure SSIS IR 可加入虛擬網路的步驟。 

## <a name="integration-runtime-location"></a>整合執行階段位置
Data Factory 位置中儲存資料處理站的中繼資料，也是觸發管道的源頭。 同時，資料處理站可以存取其他 Azure 區域的資料存放區和計算資料，以在資料存放區之間移動資料或使用計算服務處理資料。 此行為會透過[全域可用的 IR](https://azure.microsoft.com/global-infrastructure/services/) 來達成，以確保資料合規性、效率，並降低網路輸出成本。

「IR 位置」定義其後端計算的位置，基本上還會定義執行資料移動、活動分派和 SSIS 套件執行的位置。 IR 位置及其所屬的資料處理站位置可能不同。 

### <a name="azure-ir-location"></a>Azure IR 位置
您可以設定特定的 Azure IR 位置，在此情況下，資料移動或活動分派將會在該區域中執行。 

>[!TIP]
>如果您有嚴格的資料合規性需求，且必須確定資料不會離開特定地理位置，您可以明確地在特定區域中建立 Azure IR，並使用 ConnectVia 屬性將連結服務指向此 IR。 例如，如果您想要將位於英國南部的 Blob 資料複製到英國南部的 SQL DW，且想要確保資料不會離開英國，請在英國南部中建立 Azure IR，並將兩個連結服務都連結至此 IR。

如果選擇使用**自動解析 Azure IR**，則預設為， 

- 對於複製活動，ADF 將盡最大努力自動檢測接收器資料存儲的位置，然後在相同的區域（如果可用）或同一地理位置中最接近的區域中使用 IR;如果接收器資料存儲的區域無法檢測到，則使用資料工廠區域中的 IR 作為替代方法。

  例如，您在美國東部創建了工廠， 
  
  - 將資料複製到美國西部的 Azure Blob 時，如果 ADF 成功檢測到 Blob 位於美國西部，則複製活動將在美國西部的 IR 上執行;如果 ADF 成功檢測到 Blob 位於美國西部西部，則複製活動將在美國西部執行。"如果區域檢測失敗，則在美國東部的 IR 上執行複製活動。
  - 將資料複製到無法檢測到的區域的 Salesforce 時，將在美國東部的 IR 上執行複製活動。

- 對於查找/GetMetadata/刪除活動執行（也稱為管道活動）、轉換活動調度（也稱為外部活動）和創作操作（測試連接、流覽資料夾清單和表清單、預覽資料），ADF 將在資料工廠區域中使用 IR。

- 對於資料流程，ADF 將在資料工廠區域中使用 IR。 

  > [!TIP] 
  > 一種好的做法是確保資料流程與相應的資料存儲在同一區域中運行（如果可能）。 可以通過自動解析 Azure IR（如果資料存儲位置與資料工廠位置相同）或在資料存儲相同的區域創建新的 Azure IR 實例，然後執行資料流程來實現此目的。 

您可以在 UI 上的管線活動監視檢視中或活動監視承載中，監視在活動執行期間生效的 IR 位置。

### <a name="self-hosted-ir-location"></a>自我裝載 IR 位置
自我裝載 IR 在邏輯上會向 Data Factory 註冊，而用來支援其功能的計算由您提供。 因此，自我裝載 IR 沒有明確的位置屬性。 

用來執行資料移動時，自我裝載 IR 會從來源取出資料，並寫入目的地。

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR 位置
為了在擷取、轉換和下載 (ETL) 工作流程中達到高效能，務必選取正確的 Azure-SSIS IR 位置。

- Azure-SSIS IR 的位置不需要與資料工廠的位置相同，但應與要託管 SSISDB 的 Azure SQL 資料庫或託管實例伺服器的位置相同。 如此一來，您的 Azure-SSIS 整合執行階段就可以輕易存取 SSISDB，而不會在不同的位置之間產生過多流量。
- 如果沒有現有的 Azure SQL 資料庫或託管實例伺服器來承載 SSISDB，但具有本地資料來源/目標，則應在連接到本地網路的虛擬網路的同一位置創建新的 Azure SQL 資料庫或託管實例伺服器。  這樣，可以使用新的 Azure SQL 資料庫或託管實例伺服器創建 Azure-SSIS IR，並在同一位置加入該虛擬網路，從而有效地最大限度地減少了不同位置的資料移動。
- 如果託管 SSISDB 的現有 Azure SQL 資料庫或託管實例伺服器的位置與連接到本地網路的虛擬網路的位置不同，請首先使用現有的 Azure SQL 資料庫創建 Azure-SSIS IR 或託管實例伺服器並在同一位置加入另一個虛擬網路，然後將虛擬網路配置為不同位置之間的虛擬網路連接。

下圖顯示 Data Factory 及其整合執行階段的位置設定：

![整合執行階段位置](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>決定使用哪一個 IR

### <a name="copy-activity"></a>複製活動

若是複製活動，需要來源和接收連結服務來定義資料流程的方向。 下列邏輯可決定使用哪個整合執行階段執行個體來執行複製： 

- **在兩個雲端資料來源之間複製**：當來源和接收連結服務皆使用 Azure IR 時，ADF 將會在您已指定區域 Azure IR 時使用該 IR，或在您選擇自動解析 IR (預設值) 時自動判斷 Azure IR 的位置，如[整合執行階段位置](#integration-runtime-location)一節所說明。
- **在雲端資料來源與私人網路中的資料來源之間複製**：如果任一來源或接收連結服務指向自我裝載 IR，則會在自我裝載整合執行階段上執行複製活動。
- **在私人網路絡中的兩個數據源之間複製**：源和接收器連結服務必須指向集成運行時的同一實例，並且集成運行時用於執行複製活動。

### <a name="lookup-and-getmetadata-activity"></a>查閱和 GetMetadata 活動

查閱和 GetMetadata 的活動會在與資料存放區連結服務相關聯的整合執行階段上執行。

### <a name="external-transformation-activity"></a>外部轉換活動

利用外部計算引擎的每個外部轉換活動都有一個目標計算連結服務，它指向集成運行時。 此集成運行時實例確定從中調度外部手動編碼轉換活動的位置。

### <a name="data-flow-activity"></a>資料流程活動

資料流程活動在與其關聯的 Azure 集成運行時上執行。 資料流程使用的 Spark 計算由 Azure 集成運行時中的資料流程屬性決定，並由 ADF 完全管理。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [創建 Azure 集成運行時](create-azure-integration-runtime.md)
- [創建自託管集成運行時](create-self-hosted-integration-runtime.md)
- [創建 Azure-SSIS 集成運行時](create-azure-ssis-integration-runtime.md)。 這篇文章會詳述教學課程，並提供使用 Azure SQL Database 受控執行個體，以及將 IR 加入虛擬網路的指示。 
