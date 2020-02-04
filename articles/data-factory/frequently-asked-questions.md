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
ms.date: 06/27/2018
ms.openlocfilehash: 8238f2ea8395fc53044703db619d768918cb1834
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644693"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory 常見問題集
本文提供 Azure Data Factory 常見問題集的解答。  

## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？ 
Data Factory 是完全受控且以雲端為基礎的資料整合 ETL 服務，可自動化資料的移動和轉換。 就像會運轉設備以將原物料轉換成成品的工廠一樣，Azure Data Factory 會協調現有的服務來收集未經處理資料，並將其轉換成隨時可用的資訊。 

您可使用 Azure Data Factory 建立資料驅動工作流程，以在內部部署與雲端資料存放區之間移動資料。 而且您可以使用資料流程來處理和轉換資料。 ADF 也支援使用計算服務（例如 Azure HDInsight、Azure Databricks 和 SQL Server Integration Services （SSIS）整合執行時間）進行手動編碼轉換的外部計算引擎。 

透過 Data Factory，能夠在以 Azure 為基礎的雲端服務上執行資料處理，也可以使用自己的自我託管計算環境 (例如 SSIS、SQL Server 或 Oracle)。 建立可執行所需動作的管線之後，您可以將它排程為定期執行（例如每小時、每天或每週）、時間範圍排程，或從事件發生時觸發管線。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。

### <a name="control-flows-and-scale"></a>控制流程和規模 
為了支援現代化資料倉儲中的各種整合流程和模式，Data Factory 啟用彈性的資料管線模型化。 這需要完整的控制流程程式設計範例，包括條件式執行、資料管線中的分支，以及明確地在這些流程內和之間傳遞參數的能力。 控制流程也包含透過活動分派將資料轉換成外部執行引擎和資料流程功能，包括大規模的資料移動。

Data Factory 可讓您自由地為資料整合所需的任何流程樣式建立模型。這些流程樣式可依需求分派或是依排程重複分派。 以下為此模型支援的一些常見流程：   

- 控制流程：
    - 活動可以在管線內以序列方式連結在一起。
    - 活動可以在管線中分支。
    - 參數：
        - 參數可以在管線層級定義，而且您可以視需要或從觸發程式叫用管線時傳遞引數。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞：
        - 活動輸出（包括狀態）可供管線中的後續活動使用。
    - 迴圈容器：
        - Foreach 活動會在迴圈中逐一查看指定的活動集合。 
- 以觸發程序為基礎的流程：
    - 可依需求或依時鐘時間觸發管線。
- 差異流程：
    - 參數可用來定義差異複製的上限標記，同時從內部部署或雲端中的關聯式存放區移動維度或參考資料表，以將資料載入到 lake 中。 

如需詳細資訊，請參閱[教學課程：控制流程](tutorial-control-flow.md)。

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>以無程式碼管線進行大規模轉換的資料
以瀏覽器為基礎的工具可讓使用者透過新式的互動式網頁，進行無程式碼管線的撰寫與開發。

對於 visual data 開發人員和資料工程師而言，Data Factory web UI 是您將用來建立管線的無程式碼設計環境。 它與 Visual Studio Online Git 完全整合，並使用偵錯工具選項提供 CI/CD 和反復開發的整合。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>適用于 advanced 使用者的豐富跨平臺 Sdk
Data Factory V2 提供一組豐富的 Sdk，可用來使用您最愛的 IDE 來撰寫、管理及監視管線，包括：
* Python SDK
* PowerShell CLI
* C# SDK

使用者也可以使用已記載的 REST Api 來與 Data Factory V2 進行介面。

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>使用視覺化檢視進行反復式開發和偵錯工具
Azure Data Factory 視覺效果工具可啟用反復式開發和調試。 您可以使用管線畫布中的 [ **Debug** ] 功能來建立管線和執行測試回合，而不需要撰寫任何一行程式碼。 您可以在管線畫布的 [**輸出**] 視窗中，查看測試回合的結果。 在測試回合成功之後，您可以將更多活動新增至管線，並以反復方式繼續進行偵錯工具。 您也可以在測試回合進行之後將其取消。 

您不需要在選取 [ **Debug**] 之前，將變更發佈至 data factory 服務。 當您想要確定新的新增或變更會如預期般運作，然後在開發、測試或生產環境中更新您的資料處理站工作流程之前，這會很有説明。 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>將 SSIS 套件部署至 Azure 的能力 
如果您想要移動 SSIS 工作負載，可以建立 Data Factory，然後佈建 Azure-SSIS 整合執行階段。 Azure SSIS 整合執行時間是一個完全受控的 Azure Vm （節點）叢集，專門用來在雲端執行您的 SSIS 套件。 如需逐步指示，請參閱教學課程：[將 SSIS 封裝部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 
 
### <a name="sdks"></a>SDK
如果您是一位先進的使用者，而且您正在尋找程式設計介面，Data Factory 會提供一組豐富的 Sdk，讓您可以使用您最愛的 IDE 來撰寫、管理或監視管線。 .NET、PowerShell、Python 和 REST 皆提供語言支援。

### <a name="monitoring"></a>監視
您可以在瀏覽器使用者介面中透過 PowerShell、SDK 或視覺監視工具來監視自己的 Data Factory。 您可以有效率且有效的方式，監視和管理隨選、以觸發程式為基礎的自訂流程。 取消現有的工作，一眼就能看到失敗、向下切入以取得詳細的錯誤訊息，以及在不進行內容切換或在畫面之間來回流覽的單一窗格中，進行這些問題的分析。 

### <a name="new-features-for-ssis-in-data-factory"></a>Data Factory 中 SSIS 的新功能
自2017起的初始公開預覽版本，Data Factory 已新增 SSIS 的下列功能：

-   支援三個以上的設定/變體 Azure SQL Database 來裝載專案/套件的 SSIS 資料庫（SSISDB）：
-   具有虛擬網路服務端點的 SQL Database
-   受控執行個體
-   彈性集區
-   支援在傳統虛擬網路上的 Azure Resource Manager 虛擬網路于未來淘汰，這可讓您將 Azure SSIS 整合執行時間插入/加入至設定為使用虛擬網路服務端點/MI/內部部署資料存取之 SQL Database 的虛擬網路。 如需詳細資訊，請參閱將[AZURE SSIS 整合執行時間加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。
-   支援 Azure Active Directory （Azure AD）驗證和 SQL 驗證以連線至 SSISDB，允許使用 Azure 資源的 Data Factory 受控識別進行 Azure AD 驗證
-   支援將您自己的內部部署 SQL Server 授權，從 Azure Hybrid Benefit 選項獲得可觀的成本節約
-   支援 Enterprise Edition 的 Azure SSIS 整合執行時間，可讓您使用 advanced/premium 功能、自訂安裝程式介面來安裝其他元件/延伸模組，以及合作夥伴生態系統。 如需詳細資訊，請參閱[Enterprise Edition、自訂安裝，以及 ADF 中 SSIS 的協力廠商](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)擴充性。 
-   Data Factory 中的 SSIS 深入整合，可讓您在 Data Factory 管線中叫用/觸發第一級的執行 SSIS 套件活動，並透過 SSMS 加以排程。 如需詳細資訊，請參閱[在 ADF 管線中使用 SSIS 活動現代化和擴充您的 ETL/ELT 工作流程](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)。


## <a name="what-is-the-integration-runtime"></a>什麼是整合執行時間？
整合執行時間是計算基礎結構，Azure Data Factory 用來提供跨各種網路環境的下列資料整合功能：

- **資料移動**：針對資料移動，整合執行時間會在來源與目的地資料存放區之間移動資料，同時提供內建連接器、格式轉換、資料行對應，以及高效能且可調整規模的資料傳輸支援。
- **分派活動**：針對轉換，整合執行時間提供可原生執行 SSIS 套件的功能。
- **執行 SSIS 套件**：整合執行時間會在受控 Azure 計算環境中，以原生方式執行 ssis 套件。 整合執行時間也支援分派和監視在各種計算服務上執行的轉換活動，例如 Azure HDInsight、Azure Machine Learning、SQL Database 和 SQL Server。

您可以視需要部署一或多個整合執行時間實例，以移動和轉換資料。 整合執行時間可以在 Azure 公用網路上或在私人網路（內部部署、Azure 虛擬網路或 Amazon Web Services 虛擬私用雲端 [VPC]）上執行。 

如需詳細資訊，請參閱 [Azure Data Factory 中的整合執行階段](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>整合執行階段數目的限制為何？
您在資料處理站中可擁有多少個整合執行階段個體，並無硬性限制。 不過，整合執行階段可根據訂用帳戶用於 SSIS 套件封裝的虛擬機器核心數目，將會受到限制。 如需詳細資訊，請參閱 [Data Factory 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory 的最上層概念為何？
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipelines"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 其中的優勢在於，您可使用管線按組別管理活動，而無須個別管理每個活動。 您可將管線中的活動鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="data-flows"></a>資料流程
資料流程是您以視覺方式建立的物件，可在後端 Spark 服務上大規模轉換資料的 Data Factory。 您不需要瞭解程式設計或 Spark 內部。 您只需使用圖形（對應）或試算表（整頓）來設計資料轉換意圖。

### <a name="activities"></a>活動
活動代表管線中的處理步驟。 例如，您可以使用複製活動，將資料從一個資料存放區複製到另一個資料存放區。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 以這種方式思考：連結的服務會定義與資料來源的連接，而資料集代表資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 而 Azure blob 資料集會指定包含資料的 blob 容器和資料夾。

Data Factory 中的連結服務，有兩個用途：

- 用來代表*資料存放區*，其包含 (但不限於) 內部部署 SQL Server 執行個體、Oracle 資料庫執行個體、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。
- 用來代表可裝載活動執行的 *計算資源* 。 例如，HDInsight Hive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和受支援計算環境的清單，請參閱 [Azure Data Factory 中的資料轉換](transform-data.md)。

### <a name="triggers"></a>觸發程序
觸發程序代表用來決定何時需要啟動管線執行的處理單位。 針對不同類型的事件，有不同類型的觸發程序。 

### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 通常會藉由將引數傳遞給管線中定義的參數，來具現化管線執行。 您可以藉由手動方式，或是在觸發程序定義內傳遞引數。

### <a name="parameters"></a>參數
參數是唯讀設定的機碼值組。 您可以在管線中定義參數，並在執行內容期間，傳遞已定義參數的引數。 執行內容是由觸發程序，或是您手動執行的管線所建立。 管線內的活動會取用參數值。

資料集是強型別參數，以及您可以重複使用或參考的實體。 活動可以參考資料集，而且可以取用資料集定義中所定義的屬性。

連結服務亦是一種強型別參數，其包含資料存放區或計算環境的連線資訊。 它也是您可以重複使用或參考的實體。

### <a name="control-flows"></a>控制流程
控制流程負責協調管線活動，其中包括將活動循序鏈結、分支，以及可在管線層級定義的參數，還有依需求或從觸發程序叫用管線時所傳遞的引數。 控制流程也包括自訂狀態傳遞和迴圈容器（也就是 foreach 反覆運算器）。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管線和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory 的定價模型為何？
請參閱 [Data Factory 定價詳細資料](https://azure.microsoft.com/pricing/details/data-factory/)頁面，以瞭解 Azure Data Factory 的定價詳細資料。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何掌握 Data Factory 的最新資訊？
移至下列網站掌握 Azure Data Factory 的最新資訊：

- [部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文件首頁](/azure/data-factory)
- [產品首頁](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>深入技術性討論 

### <a name="how-can-i-schedule-a-pipeline"></a>如何排程管線？ 
您可以利用排程器觸發程序，或時間範圍觸發程序來排程管線。 此觸發程式會使用時鐘行事曆排程，其可定期排程管線或以行事曆為基礎的週期性模式（例如，在星期一下午6:00，而星期四位於 9:00 PM）。 如需詳細資訊，請參閱[管道執行和觸發程序](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>可以將參數傳遞給執行的管線嗎？
是，參數是 Data Factory 中的第一級最上層概念。 您可定義管線層級的參數並傳遞引數，同時依需求或使用觸發程序來執行管線執行。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>可以定義管線參數的預設值嗎？ 
可以。 您可以定義管線中參數的預設值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管線中的活動可否取用傳遞給管線執行的引數？ 
可以。 管線內的每個活動皆可使用 `@parameter` 建構，來取用傳遞給管線執行的參數值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>可以在另一個活動中取用活動輸出屬性？ 
可以。 可以使用 `@activity` 建構在後續活動中取用活動輸出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何正常處理活動輸出中的 Null 值？ 
您可以在運算式中使用 `@coalesce` 建構來正常處理 Null 值。 

## <a name="mapping-data-flows"></a>對應資料流程

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>我需要疑難排解我的資料流程邏輯的協助。 我需要提供哪些資訊才能取得協助？

當 Microsoft 提供資料流程的協助或疑難排解時，請提供資料流程腳本。 這是來自您的資料流程圖形的程式碼後置腳本。 從 ADF UI 中，開啟您的資料流程，然後按一下右上角的 [腳本] 按鈕。 複製並貼上此腳本，或將它儲存在文字檔中。

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>如何? 使用 Data Factory 中的其他90資料集類型來存取資料嗎？

對應資料流程功能目前允許從 Azure Blob 儲存體或 Azure Data Lake Storage Gen2 Azure SQL Database、Azure SQL 資料倉儲、分隔的文字檔，以及從 Blob 儲存體 Parquet 檔案，或針對來源和接收以原生方式 Data Lake Storage Gen2 的檔案。 

使用 [複製] 活動從任何其他連接器暫存資料，然後執行 [資料流程] 活動，以在暫存資料後加以轉換。 例如，您的管線會先複製到 Blob 儲存體，然後資料流程活動會使用來源中的資料集來轉換該資料。

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>自我裝載整合執行時間是否適用于資料流程？

自我裝載 IR 是一種 ADF 管線結構，您可以搭配使用複製活動，從內部內部部署或以 VM 為基礎的資料來源和接收，取得或移動資料。 請先使用複本來暫存資料，然後再以轉換的資料流程作為後續的複本，如果您需要將轉換後的資料移回內部部署存放區。

## <a name="wrangling-data-flows"></a>整頓資料流程

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>整頓資料流程支援的區域有哪些？

目前在下欄區域中建立的 data factory 支援整頓資料流程：

* 澳大利亞東部
* 加拿大中部
* 印度中部
* 美國中部
* 美國東部
* 美國東部 2
* 日本東部
* 北歐
* 東南亞
* 美國中南部
* 英國南部
* 美國中西部
* 歐洲西部
* 美國西部
* 美國西部 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>整頓資料流程有哪些限制和限制？

資料集名稱只能包含英數位元。 支援下列資料存放區：

* 使用帳戶金鑰驗證 Azure Blob 儲存體中的 DelimitedText 資料集
* 使用帳戶金鑰或服務主體驗證在 Azure Data Lake Storage gen2 中 DelimitedText 資料集
* 使用服務主體驗證在 Azure Data Lake Storage gen1 中 DelimitedText 資料集
* 使用 SQL 驗證 Azure SQL Database 和資料倉儲。 請參閱以下支援的 SQL 類型。 沒有適用于資料倉儲的 PolyBase 或預備支援。

目前，整頓資料流程中不支援連結的服務 Key Vault 整合。

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>對應和整頓資料流程之間有何差異？

對應資料流程提供了一種方法來大規模轉換資料，而不需要撰寫任何程式碼。 您可以藉由建立一連串的轉換，在資料流程畫布中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 使用接收來完成資料流程，以將您的結果放在目的地中。 對應資料流程非常適合用來在接收和來源中，以已知和未知的架構來對應和轉換資料。

整頓資料流程可讓您透過 spark 執行，使用大規模 Power Query 的線上混合式編輯器進行 agile 資料準備和探索。 隨著資料 lake 的增加，有時您只需要流覽資料集或在 lake 中建立資料集。 您未對應到已知的目標。 整頓資料流程會用於較不正式和以模型為基礎的分析案例。

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Power Platform 資料流程和整頓資料流程之間有何差異？

Power Platform 資料流程可讓使用者將各種資料來源的資料匯入和轉換成 Common Data Service，並 Azure Data Lake 來建立 PowerApps 應用程式、Power BI 報表或流程自動化。 Power Platform 資料流程使用已建立的 Power Query 資料準備體驗，類似于 Power BI 和 Excel。 Power Platform 資料流程也可讓您輕鬆地在組織內重複使用，並自動處理協調流程（例如，在重新整理前一個資料流程時，自動重新整理相依于其他資料流程的資料流程）。

Azure Data Factory （ADF）是受控資料整合服務，可讓資料工程師和公民資料整合者建立複雜的混合式「解壓縮-轉換-載入」（ETL）和「解壓縮-載入-轉換」（ELT）工作流程。 ADF 中的整頓資料流程可讓使用者擁有無程式碼的無伺服器環境，以簡化雲端中的資料準備，並調整為任何資料大小，而不需要基礎結構管理。 它會使用 Power Query 資料準備技術（也用於 Power Platform 資料流程、Excel、Power BI）來準備和塑造資料。 建立用來處理 big data 整合的所有複雜性和規模挑戰，整頓的資料流程可讓使用者透過 spark 執行快速地大規模準備資料。 使用者可以使用我們的瀏覽器型介面，在可存取的視覺環境中建立復原的資料管線，並讓 ADF 處理 Spark 執行的複雜性。 建立管線的排程，並從 ADF 監視入口網站監視您的資料流程執行。 使用 ADF 的豐富可用性監視和警示，輕鬆管理資料可用性 Sla，並利用內建的持續整合和部署功能，在受管理的環境中儲存和管理您的流程。 建立警示並查看執行計畫，以驗證您的邏輯在您微調資料流程時是否按照計畫執行。

### <a name="supported-sql-types"></a>支援的 SQL 類型

整頓資料流程支援 SQL 中的下列資料類型。 使用不支援的資料類型時，您會收到驗證錯誤。

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

- [快速入門：建立 Data Factory](quickstart-create-data-factory-dot-net.md)
- [教學課程：複製雲端中的資料](tutorial-copy-data-dot-net.md)
