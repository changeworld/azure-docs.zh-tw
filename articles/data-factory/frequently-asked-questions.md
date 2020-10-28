---
title: 'Azure Data Factory：常見問題 '
description: 獲得 Azure Data Factory 常見問題的解答。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: ba3cc376edef1e6dc8fbf859e456219a1fd3ca60
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635757"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory 常見問題集

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供 Azure Data Factory 常見問題集的解答。  

## <a name="what-is-azure-data-factory"></a>什麼是 Azure Data Factory？ 
Data Factory 是完全受控的雲端式資料整合 ETL 服務，可自動化資料的移動和轉換。 就像會運轉設備以將原物料轉換成成品的工廠一樣，Azure Data Factory 會協調現有的服務來收集未經處理資料，並將其轉換成隨時可用的資訊。 

您可使用 Azure Data Factory 建立資料驅動工作流程，以在內部部署與雲端資料存放區之間移動資料。 您可以使用資料流程來處理和轉換資料。 ADF 也支援使用計算服務（例如 Azure HDInsight、Azure Databricks 和 SQL Server Integration Services (SSIS) Integration runtime）進行手動編碼轉換的外部計算引擎。 

透過 Data Factory，能夠在以 Azure 為基礎的雲端服務上執行資料處理，也可以使用自己的自我託管計算環境 (例如 SSIS、SQL Server 或 Oracle)。 建立可執行所需動作的管線之後，您可以將它排程為定期執行 (每小時、每日或每週，例如) 、時間範圍排程，或從事件發生時觸發管線。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。

### <a name="control-flows-and-scale"></a>控制流程和規模 
為了支援現代化資料倉儲中的各種整合流程和模式，Data Factory 可提供彈性的資料管線模型。 這需要完整的控制流程程式設計架構，包括條件式執行、在資料管線中分支，以及能夠在這些流程內和之間明確傳遞參數。 控制流程也包含透過「活動分派」將資料轉換至外部執行引擎和資料流程功能，包括透過「複製活動」進行大規模資料移動。

Data Factory 可讓您自由地為資料整合所需的任何流程樣式建立模型。這些流程樣式可依需求分派或是依排程重複分派。 以下為此模型支援的一些常見流程：   

- 控制流程：
    - 活動可以在管線內的序列中連結在一起。
    - 活動可以在管線內進行分支。
    - 參數：
        - 您可以在管線層級定義參數，並可在您視需要或從觸發程式叫用管線時傳遞引數。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞：
        - 活動輸出（包括狀態）可供管線中的後續活動使用。
    - 迴圈容器：
        - Foreach 活動會在迴圈中逐一查看指定的活動集合。 
- 以觸發程序為基礎的流程：
    - 可依需求或依時鐘時間觸發管線。
- 差異流程：
    - 當您從內部部署或雲端中的關聯式存放區移動維度或參考資料表，以將資料載入至 lake 時，可使用參數來定義差異複本的高水位標記。 

如需詳細資訊，請參閱[教學課程：控制流程](tutorial-control-flow.md)。

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>使用無程式碼管線大規模轉換的資料
以瀏覽器為基礎的工具可讓使用者透過新式的互動式網頁，進行無程式碼管線的撰寫與開發。

針對 visual 資料開發人員和資料工程師，Data Factory web UI 是您將用來建立管線的無程式碼設計環境。 它已與 Visual Studio Online Git 完全整合，並提供 CI/CD 的整合，以及使用偵錯工具的反復式開發。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>適用于 advanced 使用者的豐富跨平臺 Sdk
Data Factory V2 提供一組豐富的 Sdk，可讓您使用最愛的 IDE 來撰寫、管理及監視管線，包括：
* Python SDK
* PowerShell CLI
* C# SDK

使用者也可以使用記載的 REST Api 與 Data Factory V2 互動。

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>使用視覺化檢視進行反復開發和偵錯工具
Azure Data Factory 的視覺效果工具可進行反復式開發和調試。 您可以使用管線畫布中的 **Debug** 功能來建立管線和執行測試回合，而不需要撰寫任何一行程式碼。 您可以在管線畫布的 [ **輸出** ] 視窗中，查看測試回合的結果。 當您的測試回合成功之後，您可以將更多活動新增至管線，並以反復的方式繼續進行偵錯工具。 您也可以在測試執行完成之後予以取消。 

在選取 **Debug** 之前，您不需要將變更發佈至 data factory 服務。 當您在開發、測試或生產環境中更新資料處理站工作流程之前，您想要確保新的新增專案或變更會如預期般運作，這會很有説明。 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>能夠將 SSIS 套件部署至 Azure 
如果您想要移動 SSIS 工作負載，可以建立 Data Factory，然後佈建 Azure-SSIS 整合執行階段。 Azure SSIS 整合執行時間是一個完全受控的 Azure Vm (節點) ，專門用來在雲端執行您的 SSIS 套件。 如需逐步指示，請參閱教學課程：[將 SSIS 封裝部署至 Azure](./tutorial-deploy-ssis-packages-azure.md)。 
 
### <a name="sdks"></a>SDK
如果您是先進的使用者，且正在尋找程式設計介面，Data Factory 提供一組豐富的 Sdk，讓您可以使用您最愛的 IDE 來撰寫、管理或監視管線。 .NET、PowerShell、Python 和 REST 皆提供語言支援。

### <a name="monitoring"></a>監視
您可以在瀏覽器使用者介面中透過 PowerShell、SDK 或視覺監視工具來監視自己的 Data Factory。 您可以透過有效率且有效的方式，監視和管理依需求、觸發程式和時鐘導向的自訂流程。 取消現有的工作、一眼就能看到失敗、向下切入以取得詳細的錯誤訊息，以及從單一的畫面格進行檢查以取得詳細的錯誤訊息，而不需要在畫面之間來回切換或來回流覽。 

### <a name="new-features-for-ssis-in-data-factory"></a>Data Factory 中的 SSIS 新功能
自2017版的初始公開預覽版本起，Data Factory 已為 SSIS 新增下列功能：

-    支援 Azure SQL Database 的三個設定/變體，以裝載 SSIS 資料庫 (專案/套件的 SSISDB) ：
-    使用虛擬網路服務端點 SQL Database
-    SQL 受控執行個體
-    彈性集區
-    在傳統虛擬網路上的 Azure Resource Manager 虛擬網路支援即將在未來淘汰，可讓您將 Azure SSIS 整合執行時間插入/加入至為具有虛擬網路服務端點/MI/內部部署資料存取的 SQL Database 設定的虛擬網路。 如需詳細資訊，請參閱將 [AZURE SSIS 整合執行時間加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。
-    支援 Azure Active Directory (Azure AD) 驗證和 SQL 驗證來連線到 SSISDB，以允許 Azure AD 使用 Azure 資源的受控識別進行驗證
-    支援讓現有的 SQL Server 授權從 Azure Hybrid Benefit 選項獲得可觀的成本節約
-    Azure SSIS 整合執行時間的 Enterprise Edition 支援，可讓您使用 advanced/premium 功能、可安裝其他元件/擴充功能的自訂安裝介面，以及合作夥伴生態系統。 如需詳細資訊，請參閱 [ADF 中 SSIS 的 Enterprise Edition、自訂設定和協力廠商](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)擴充性。 
-    Data Factory 中的 SSIS 更深層整合，可讓您在 Data Factory 管線中叫用/觸發第一級的 Execute SSIS 套件活動，並透過 SSMS 進行排程。 如需詳細資訊，請參閱 [將您的 ETL/ELT 工作流程現代化並擴充至 ADF 管線中的 SSIS 活動](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)。


## <a name="what-is-the-integration-runtime"></a>什麼是整合執行時間？
整合執行時間是一種計算基礎結構，Azure Data Factory 用來跨各種網路環境提供下列資料整合功能：

- **資料移動** ：針對資料移動，整合執行時間會在來源與目的地資料存放區之間移動資料，同時提供內建連接器、格式轉換、資料行對應，以及高效能且可調整規模的資料傳輸支援。
- **分派活動** ：針對轉換，整合執行時間提供原生執行 SSIS 套件的功能。
- **執行 ssis 套件** ：整合執行時間會以原生方式在受控 Azure 計算環境中執行 SSIS 套件。 整合執行時間也支援分派和監視在各種計算服務上執行的轉換活動，例如 Azure HDInsight、Azure Machine Learning、SQL Database 和 SQL Server。

您可以視需要部署一或多個整合執行時間實例，以移動和轉換資料。 整合執行時間可以在 Azure 公用網路或私人網路上 (內部部署、Azure 虛擬網路或 Amazon Web Services 虛擬私用雲端 [VPC] ) 上執行。 

如需詳細資訊，請參閱 [Azure Data Factory 中的整合執行階段](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>整合執行階段數目的限制為何？
您在資料處理站中可擁有多少個整合執行階段個體，並無硬性限制。 不過，整合執行階段可根據訂用帳戶用於 SSIS 套件封裝的虛擬機器核心數目，將會受到限制。 如需詳細資訊，請參閱 [Data Factory 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory 的最上層概念為何？
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipelines"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 其中的優勢在於，您可使用管線按組別管理活動，而無須個別管理每個活動。 您可將管線中的活動鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="data-flows"></a>資料流程
資料流程是您以視覺化方式在 Data Factory 中建立的物件，可在後端 Spark 服務上大規模轉換資料。 您不需要瞭解程式設計或 Spark 內部。 您只需使用圖形 (對應) 或試算表 (整頓) ，來設計您的資料轉換意圖。

### <a name="activities"></a>活動
活動代表管線中的處理步驟。 例如，您可以使用複製活動，將資料從某個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 以這種方式思考：連結的服務會定義與資料來源的連接，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 而且，Azure blob 資料集會指定包含資料的 blob 容器和資料夾。

Data Factory 中的連結服務，有兩個用途：

- 表示包含（但不限於） SQL Server 實例、Oracle 資料庫實例、檔案共用或 Azure Blob 儲存體帳戶的 *資料存放區* 。 如需支援的資料存放區清單，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。
- 用來代表可裝載活動執行的 *計算資源* 。 例如，HDInsight Hive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和受支援計算環境的清單，請參閱 [Azure Data Factory 中的資料轉換](transform-data.md)。

### <a name="triggers"></a>觸發程序
觸發程序代表用來決定何時需要啟動管線執行的處理單位。 針對不同類型的事件，有不同類型的觸發程序。 

### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 通常會藉由將引數傳遞給管線中定義的參數，來具現化管線執行。 您可以藉由手動方式，或是在觸發程序定義內傳遞引數。

### <a name="parameters"></a>參數
參數是唯讀設定的機碼值組。您可在管線中定義參數，並在執行內容的過程中傳遞已定義參數的引數。 執行內容是由觸發程序，或是您手動執行的管線所建立。 管線內的活動會取用參數值。

Dataset 是強型別參數，以及您可以重複使用或參考的實體。 活動可以參考資料集，也可以取用資料集定義中所定義的屬性。

連結服務亦是一種強型別參數，其包含資料存放區或計算環境的連線資訊。 它也是您可以重複使用或參考的實體。

### <a name="control-flows"></a>控制流程
控制流程負責協調管線活動，其中包括將活動循序鏈結、分支，以及可在管線層級定義的參數，還有依需求或從觸發程序叫用管線時所傳遞的引數。 控制流程也包含自訂狀態傳遞和迴圈容器 (也就是 foreach 反覆運算器) 。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管線和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory 的定價模型為何？
請參閱 [Data Factory 定價詳細資料](https://azure.microsoft.com/pricing/details/data-factory/)頁面，以瞭解 Azure Data Factory 的定價詳細資料。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何掌握 Data Factory 的最新資訊？
移至下列網站掌握 Azure Data Factory 的最新資訊：

- [部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文件首頁](./index.yml)
- [產品首頁](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>深入技術性討論 

### <a name="how-can-i-schedule-a-pipeline"></a>如何排程管線？ 
您可以利用排程器觸發程序，或時間範圍觸發程序來排程管線。 此觸發程式會使用時鐘日曆排程，以定期排程管線或以行事曆為基礎的週期性模式進行排程 (例如，在星期一下午6:00，以及下午9:00 星期四) 。 如需詳細資訊，請參閱[管道執行和觸發程序](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>可以將參數傳遞給執行的管線嗎？
是，參數是 Data Factory 中的第一級最上層概念。 您可定義管線層級的參數並傳遞引數，同時依需求或使用觸發程序來執行管線執行。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>可以定義管線參數的預設值嗎？ 
是。 您可以定義管線中參數的預設值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管線中的活動可否取用傳遞給管線執行的引數？ 
是。 管線內的每個活動皆可使用 `@parameter` 建構，來取用傳遞給管線執行的參數值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>可以在另一個活動中取用活動輸出屬性？ 
是。 可以使用 `@activity` 建構在後續活動中取用活動輸出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何正常處理活動輸出中的 Null 值？ 
您可以在運算式中使用 `@coalesce` 建構來正常處理 Null 值。 

## <a name="mapping-data-flows"></a>對應資料流程

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>我需要針對資料流程邏輯進行疑難排解的協助。 我需要提供什麼資訊以取得協助？

當 Microsoft 針對資料流程提供協助或疑難排解時，請提供資料流程腳本。 這是來自您的資料流程圖的程式碼後端腳本。 從 ADF UI 開啟您的資料流程，然後按一下右上角的 [腳本] 按鈕。 複製並貼上此腳本，或將它儲存在文字檔中。

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>如何? 在 Data Factory 中使用其他90資料集類型來存取資料嗎？

對應資料流程功能目前允許 Azure SQL Database、Azure Synapse Analytics (先前的 SQL 資料倉儲) 、Azure Blob 儲存體或 Azure Data Lake Storage Gen2 中的分隔文字檔，以及從 Blob 儲存體 Parquet 檔案，或以原生方式為來源和接收進行 Data Lake Storage Gen2。 

使用複製活動來暫存任何其他連接器的資料，然後執行資料流程活動以在暫存資料之後進行轉換。 例如，您的管線會先複製到 Blob 儲存體，然後資料流程活動會使用來源中的資料集來轉換該資料。

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>自我裝載的整合執行時間是否可用於資料流程？

自我裝載 IR 是您可以搭配複製活動使用的 ADF 管線結構，可在內部內部部署或以 VM 為基礎的資料來源和接收之間取得或移動資料。 先使用複製來暫存資料，然後使用資料流程進行轉換，然後在您需要將已轉換的資料移回內部內部部署存放區時進行後續的複製。

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>資料流程計算引擎是否會為多個租使用者提供服務？
永遠不會共用叢集。 我們保證會在實際執行執行中執行每個工作的隔離。 在 debug 案例中，一個人會取得一個叢集，而所有的偵錯工具都會移至該使用者所起始的叢集。

## <a name="wrangling-data-flows"></a>整頓資料流程

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>整頓資料流程支援哪些區域？

在下欄區域中建立的資料處理站目前支援整頓資料流程：

* 澳大利亞東部
* 加拿大中部
* 印度中部
* 美國東部
* 美國東部 2
* 日本東部
* 北歐
* 東南亞
* 美國中南部
* 英國南部
* 美國中西部
* 西歐
* 美國西部
* 美國西部 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>整頓資料流程有哪些限制和條件約束？

資料集名稱只可包含英數位元。 支援下列資料存放區：

* 使用帳戶金鑰驗證 Azure Blob 儲存體中的 DelimitedText 資料集
* 使用帳戶金鑰或服務主體驗證 Azure Data Lake Storage gen2 中的 DelimitedText 資料集
* 使用服務主體驗證 Azure Data Lake Storage gen1 中的 DelimitedText 資料集
* 使用 SQL 驗證的 Azure SQL Database 和資料倉儲。 請參閱下列支援的 SQL 類型。 沒有適用于資料倉儲的 PolyBase 或預備環境支援。

目前，整頓資料流程中不支援連結服務 Key Vault 整合。

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>對應和整頓資料流程之間有何差異？

對應資料流程提供一種方式來大規模轉換資料，而不需要撰寫任何程式碼。 您可以藉由建立一連串的轉換，在資料流程畫布中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 使用接收器完成您的資料流程，以將結果置入目的地。 對應資料流程非常適合用來在接收和來源中對應和轉換具有已知和未知架構的資料。

整頓資料流程可讓您透過 spark 執行，大規模地使用 Power Query 線上混搭編輯器來進行敏捷式資料準備和探索。 隨著資料 lake 的增加，有時候您只需要探索資料集，或在 lake 中建立資料集。 您未對應到已知的目標。 整頓資料流程用於較不正式和以模型為基礎的分析案例。

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Power Platform 資料流程和整頓資料流程之間有何差異？

Power Platform 資料流程可讓使用者從各種資料來源將資料匯入和轉換成 Common Data Service 和 Azure Data Lake，以建立 PowerApps 應用程式、Power BI 報表或流程自動化。 Power Platform 資料流程使用已建立的 Power Query 資料準備體驗，類似于 Power BI 與 Excel。 Power Platform 資料流程也可讓您輕鬆地在組織內重複使用，並自動處理協調流程 (例如，當先前的資料流程) 重新整理時，會自動重新整理相依于另一個資料流程的資料流程。

Azure Data Factory (ADF) 是受控資料整合服務，可讓資料工程師和公民資料整合者建立複雜的混合式解壓縮-轉換-載入 (ETL) 和解壓縮-載入轉換 (ELT) 工作流程。 ADF 中的整頓資料流程可讓使用者使用無程式碼的無伺服器環境，以簡化雲端中的資料準備工作，並在不需要基礎結構管理的情況下調整為任何資料大小。 它使用 Power Query 的資料準備技術 (也用於 Power Platform 資料流程、Excel、Power BI) ，以準備和塑造資料。 整頓資料流程可讓使用者透過 spark 執行，快速地大規模準備資料，以處理大型資料整合的所有複雜性和大規模挑戰。 使用者可以透過以瀏覽器為基礎的介面，在可存取的視覺環境中建立具復原性的資料管線，並讓 ADF 處理 Spark 執行的複雜度。 建立管線的排程，並監視 ADF 監視入口網站的資料流程執行。 使用 ADF 的豐富可用性監視和警示輕鬆管理資料可用性 Sla，並利用內建的持續整合和部署功能，在受控環境中儲存和管理您的流程。 建立警示和查看執行計畫，以驗證您的邏輯會在您微調資料流程時以計畫的方式執行。

### <a name="supported-sql-types"></a>支援的 SQL 類型

在 SQL 中，整頓資料流程支援下列資料類型。 若使用不支援的資料類型，您將會收到驗證錯誤。

* short
* double
* real
* FLOAT
* char
* NCHAR
* varchar
* NVARCHAR
* integer
* int
* bit
* boolean
* SMALLINT
* TINYINT
* BIGINT
* long
* text
* date
* Datetime
* datetime2
* smalldatetime
* timestamp
* UNIQUEIDENTIFIER
* Xml

未來將會支援其他資料類型。

## <a name="next-steps"></a>後續步驟
如需建立資料處理站的逐步指示，請參閱下列教學課程：

- [快速入門：建立 data factory](quickstart-create-data-factory-dot-net.md)
- [教學課程：複製雲端中的資料](tutorial-copy-data-dot-net.md)