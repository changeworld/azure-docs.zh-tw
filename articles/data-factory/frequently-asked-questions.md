---
title: 'Azure 資料工廠：常見問題 '
description: 獲得 Azure Data Factory 常見問題的解答。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 34972e70039fef17161bdef66f64278cbabf908f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130808"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory 常見問題集
本文提供 Azure Data Factory 常見問題集的解答。  

## <a name="what-is-azure-data-factory"></a>Azure 資料處理站是什麼？ 
資料工廠是一種完全託管、基於雲的資料整合 ETL 服務，可自動移動和轉換資料。 就像會運轉設備以將原物料轉換成成品的工廠一樣，Azure Data Factory 會協調現有的服務來收集未經處理資料，並將其轉換成隨時可用的資訊。 

您可使用 Azure Data Factory 建立資料驅動工作流程，以在內部部署與雲端資料存放區之間移動資料。 您還可以使用資料流程處理和轉換資料。 ADF 還通過使用計算服務（如 Azure HDInsight、Azure 資料塊和 SQL 伺服器整合服務 （SSIS） 集成運行時）支援用於手動編碼轉換的外部計算引擎。 

透過 Data Factory，能夠在以 Azure 為基礎的雲端服務上執行資料處理，也可以使用自己的自我託管計算環境 (例如 SSIS、SQL Server 或 Oracle)。 創建執行所需操作的管道後，可以計畫定期運行（例如每小時、每天或每週）、時間視窗計畫或從事件發生觸發管道。 如需詳細資訊，請參閱 [Azure Data Factory 簡介](introduction.md)。

### <a name="control-flows-and-scale"></a>控制流程和規模 
為了支援現代資料倉儲中的各種集成流和模式，資料工廠支援靈活的資料管道建模。 這需要完全控制流程式設計範例，包括條件執行、資料管道中的分支，以及在這些流內和跨這些流中顯式傳遞參數的能力。 控制流還包括通過活動調度將資料轉換為外部執行引擎和資料流程功能，包括通過複製活動進行大規模資料移動。

Data Factory 可讓您自由地為資料整合所需的任何流程樣式建立模型。這些流程樣式可依需求分派或是依排程重複分派。 以下為此模型支援的一些常見流程：   

- 控制流程：
    - 活動可以按管道中的序列連結在一起。
    - 活動可以在管道中分支。
    - 參數：
        - 參數可以在管道級別定義，在按需調用管道或從觸發器調用管道時，可以傳遞參數。
        - 活動可以取用傳遞給管線的引數。
    - 自訂狀態傳遞：
        - 活動輸出（包括狀態）可由管道中的後續活動使用。
    - 迴圈容器：
        - foreach 活動將反覆運算迴圈中指定的活動集合。 
- 以觸發程序為基礎的流程：
    - 可依需求或依時鐘時間觸發管線。
- 差異流程：
    - 參數可用於定義增量複製的高水位線，同時從關係存儲（本地或雲中）移動維度或參考表以將資料載入到湖中。 

如需詳細資訊，請參閱[教學課程：控制流程](tutorial-control-flow.md)。

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>使用無代碼管道大規模轉換的資料
以瀏覽器為基礎的工具可讓使用者透過新式的互動式網頁，進行無程式碼管線的撰寫與開發。

對於視覺化資料開發人員和資料工程師，資料工廠 Web UI 是用於構建管道的無代碼設計環境。 它與 Visual Studio 線上 Git 完全集成，為 CI/CD 和反覆開發提供調試選項的集成。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>為高級使用者提供豐富的跨平臺 SDK
資料工廠 V2 提供了一組豐富的 SDK，可用於使用您喜愛的 IDE 創作、管理和監視管道，包括：
* Python SDK
* 強力殼牌 CLI
* C# SDK

使用者還可以使用記錄到的 REST API 與資料工廠 V2 進行介面。

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>使用視覺化檢視反覆開發和調試
Azure 資料工廠視覺化檢視支援反覆開發和調試。 您可以使用管道畫布中的**調試**功能創建管道並執行測試回合，而無需編寫一行代碼。 您可以在管道畫布的 **"輸出"** 視窗中查看測試結果。 測試回合成功後，可以向管道添加更多活動，並繼續以反覆運算方式進行調試。 您還可以在測試回合正在進行後取消測試回合。 

在選擇**調試**之前，您不需要將更改發佈到資料工廠服務。 這在您希望確保在開發、測試或生產環境中更新資料工廠工作流之前按預期工作的情況中非常有用。 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>能夠將 SSIS 包部署到 Azure 
如果您想要移動 SSIS 工作負載，可以建立 Data Factory，然後佈建 Azure-SSIS 整合執行階段。 Azure-SSIS 集成運行時是 Azure VM（節點）的完全託管群集，專用於在雲中運行 SSIS 包。 如需逐步指示，請參閱教學課程：[將 SSIS 封裝部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 
 
### <a name="sdks"></a>SDK
如果您是高級使用者，並且正在查找程式設計介面，資料工廠提供了一組豐富的 SDK，您可以使用這些 SDK 使用您喜愛的 IDE 來創作、管理或監視管道。 .NET、PowerShell、Python 和 REST 皆提供語言支援。

### <a name="monitoring"></a>監視
您可以在瀏覽器使用者介面中透過 PowerShell、SDK 或視覺監視工具來監視自己的 Data Factory。 您可以高效有效地監視和管理按需、基於觸發器和時鐘驅動的自訂流。 取消現有任務，一目了然地查看故障，向下切入以獲取詳細的錯誤訊息，並調試問題，所有這些都來自單個窗格，無需上下文切換或在螢幕之間來回導航。 

### <a name="new-features-for-ssis-in-data-factory"></a>資料工廠中 SSIS 的新功能
自 2017 年首次公開預覽發佈以來，資料工廠為 SSIS 添加了以下功能：

-    支援 Azure SQL 資料庫的三個配置/變體，以承載專案/包的 SSIS 資料庫 （SSISDB）：
-    具有虛擬網路服務終結點的 SQL 資料庫
-    受控執行個體
-    彈性集區
-    支援將來棄用的經典虛擬網路之上的 Azure 資源管理器虛擬網路，這允許您將 Azure-SSIS 集成運行時注入/加入為 SQL 資料庫配置的虛擬網路，並具有虛擬網路服務終結點/MI/本地資料訪問。 有關詳細資訊，請參閱[將 Azure-SSIS 集成運行時加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。
-    支援 Azure 活動目錄 （Azure AD） 身份驗證和 SQL 身份驗證以連接到 SSISDB，允許 Azure AD 使用 Azure 資源的資料工廠託管標識進行 Azure AD 身份驗證
-    支援自帶本地 SQL Server 許可證，以便從 Azure 混合權益選項中節省大量成本
-    支援 Azure-SSIS 集成運行時的企業版，允許您使用高級/高級功能、用於安裝其他元件/擴展的自訂設置介面以及合作夥伴生態系統。 有關詳細資訊，請參閱[ADF 中 SSIS 的企業版、自訂設置和協力廠商可擴充性](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)。 
-    在資料工廠中更深入地集成 SSIS，允許您在資料工廠管道中調用/觸發一流的執行 SSIS 包活動，並通過 SSMS 進行計畫。 有關詳細資訊，請參閱[在 ADF 管道中使用 SSIS 活動實現 ETL/ELT 工作流的現代化和擴展](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)。


## <a name="what-is-the-integration-runtime"></a>什麼是集成運行時？
集成運行時是 Azure 資料工廠用於跨各種網路環境提供以下資料整合功能的計算基礎結構：

- **資料移動**：對於資料移動，集成運行時在來源資料存儲和目標資料存儲之間移動資料，同時支援內置連接器、格式轉換、列映射以及執行和可擴展的資料傳輸。
- **調度活動**：對於轉換，集成運行時提供了本機執行 SSIS 包的功能。
- **執行 SSIS 包**：集成運行時本機在託管 Azure 計算環境中執行 SSIS 包。 集成運行時還支援在各種計算服務（如 Azure HDInsight、Azure 機器學習、SQL 資料庫和 SQL Server）上運行的調度和監視轉換活動。

您可以根據需要部署一個或多個集成運行時的實例來移動和轉換資料。 集成運行時可以在 Azure 公用網路或私人網路絡（本地、Azure 虛擬網路或 Amazon Web 服務虛擬私有雲 [VPC]）上運行。 

有關詳細資訊，請參閱[Azure 資料工廠中的集成運行時](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>整合執行階段數目的限制為何？
您在資料處理站中可擁有多少個整合執行階段個體，並無硬性限制。 不過，整合執行階段可根據訂用帳戶用於 SSIS 套件封裝的虛擬機器核心數目，將會受到限制。 如需詳細資訊，請參閱 [Data Factory 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory 的最上層概念為何？
Azure 訂用帳戶可能會有一或多個 Azure Data Factory 執行個體 (或資料處理站)。 Azure Data Factory 是由四個重要元件所組成，這些元件會一起運作，以提供平台讓您撰寫具有資料移動和轉換步驟的資料驅動工作流程。

### <a name="pipelines"></a>管線
資料處理站可以有一或多個管線。 管線是一個執行某個單位工作的活動邏輯群組。 管線中的活動會合作執行一項工作。 例如，管線可能包含一組活動，以從 Azure Blob 內嵌資料，然後對 HDInsight 叢集執行 Hive 查詢來分割資料。 其中的優勢在於，您可使用管線按組別管理活動，而無須個別管理每個活動。 您可將管線中的活動鏈結在一起以循序方式運作，或是以平行方式獨立運作。

### <a name="data-flows"></a>資料流程
資料流程是在資料工廠中直觀地構建的物件，用於在後端 Spark 服務上大規模轉換資料。 您無需瞭解程式設計或 Spark 內部。 只需使用圖形（映射）或試算表（Wrangling）設計資料轉換意圖。

### <a name="activities"></a>活動
活動代表管線中的處理步驟。 例如，可以使用"複製"活動將資料從一個資料存儲複製到另一個資料存儲。 同樣地，您可以使用在 Azure HDInsight 叢集上執行 Hive 查詢的 Hive 活動，來轉換或分析您的資料。 Data Factory 支援三種類型的活動︰資料移動活動、資料轉換活動，以及控制活動。

### <a name="datasets"></a>資料集
資料集代表資料存放區中的資料結構，其只會指出或參考您要在活動中作為輸入或輸出的資料。 

### <a name="linked-services"></a>連結的服務
已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這樣可以這樣考慮：連結的服務定義與資料來源的連接，資料集表示資料的結構。 例如，Azure 儲存體連結的服務會指定連接字串以連線到 Azure 儲存體帳戶。 Azure Blob 資料集指定 Blob 容器和包含資料的資料夾。

Data Factory 中的連結服務，有兩個用途：

- 用來代表*資料存放區*，其包含 (但不限於) 內部部署 SQL Server 執行個體、Oracle 資料庫執行個體、檔案共用或 Azure Blob 儲存體帳戶。 如需支援的資料存放區清單，請參閱 [Azure Data Factory 中的複製活動](copy-activity-overview.md)。
- 用來代表可裝載活動執行的 *計算資源* 。 例如，HDInsight Hive 活動會在 HDInsight Hadoop 叢集上執行。 如需轉換活動和受支援計算環境的清單，請參閱 [Azure Data Factory 中的資料轉換](transform-data.md)。

### <a name="triggers"></a>觸發程序
觸發程序代表用來決定何時需要啟動管線執行的處理單位。 針對不同類型的事件，有不同類型的觸發程序。 

### <a name="pipeline-runs"></a>管線執行
管線執行是管線執行的執行個體。 通常會藉由將引數傳遞給管線中定義的參數，來具現化管線執行。 您可以藉由手動方式，或是在觸發程序定義內傳遞引數。

### <a name="parameters"></a>參數
參數是唯讀設定的機碼值組。您可在管線中定義參數，並在執行內容的過程中傳遞已定義參數的引數。 執行內容是由觸發程序，或是您手動執行的管線所建立。 管線內的活動會取用參數值。

資料集是強型別參數，是可以重用或引用的實體。 活動可以參考資料集，並且可以使用資料集定義中定義的屬性。

連結服務亦是一種強型別參數，其包含資料存放區或計算環境的連線資訊。 它也是您可以重用或引用的實體。

### <a name="control-flows"></a>控制流程
控制流程負責協調管線活動，其中包括將活動循序鏈結、分支，以及可在管線層級定義的參數，還有依需求或從觸發程序叫用管線時所傳遞的引數。 控制流還包括自訂狀態傳遞和迴圈容器（即每個反覆運算器的啟動器）。


如需 Data Factory 概念的詳細資訊，請參閱下列文章：

- [資料集和連結服務](concepts-datasets-linked-services.md)
- [管道和活動](concepts-pipelines-activities.md)
- [整合執行階段](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory 的定價模型為何？
請參閱 [Data Factory 定價詳細資料](https://azure.microsoft.com/pricing/details/data-factory/)頁面，以瞭解 Azure Data Factory 的定價詳細資料。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何掌握 Data Factory 的最新資訊？
移至下列網站掌握 Azure Data Factory 的最新資訊：

- [博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文件首頁](/azure/data-factory)
- [產品首頁](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>深入技術性討論 

### <a name="how-can-i-schedule-a-pipeline"></a>如何排程管線？ 
您可以利用排程器觸發程序，或時間範圍觸發程序來排程管線。 觸發器使用掛鐘日曆計畫，可以定期或以基於日曆的迴圈模式（例如，週一下午 6：00 和星期四晚上 9：00）計畫管道。 如需詳細資訊，請參閱[管道執行和觸發程序](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>可以將參數傳遞給執行的管線嗎？
是的，參數是資料工廠中一流的頂級概念。 您可定義管線層級的參數並傳遞引數，同時依需求或使用觸發程序來執行管線執行。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>可以定義管線參數的預設值嗎？ 
是。 您可以定義管線中參數的預設值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管線中的活動可否取用傳遞給管線執行的引數？ 
是。 管線內的每個活動皆可使用 `@parameter` 建構，來取用傳遞給管線執行的參數值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>可以在另一個活動中取用活動輸出屬性？ 
是。 可以使用 `@activity` 建構在後續活動中取用活動輸出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何正常處理活動輸出中的 Null 值？ 
您可以在運算式中使用 `@coalesce` 建構來正常處理 Null 值。 

## <a name="mapping-data-flows"></a>對應資料流程

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>我需要幫助解決資料流程邏輯。 需要提供哪些資訊才能獲得説明？

當 Microsoft 提供資料流程的説明或故障排除時，請提供資料流程腳本。 這是資料流程圖中的代碼後面腳本。 從 ADF UI 打開資料流程，然後按一下右上角的"腳本"按鈕。 複製並粘貼此腳本或將其保存在文字檔中。

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>如何使用資料工廠中的其他 90 種資料集類型訪問資料？

映射資料流程功能當前允許 Azure SQL 資料庫、Azure SQL 資料倉儲、從 Azure Blob 存儲或 Azure 資料存儲庫第 2 代中分隔文字檔，以及從 Blob 存儲或資料湖存儲 Gen2 中限定文字檔，用於源和接收器。 

使用 Copy 活動暫存來自任何其他連接器的資料，然後在暫存資料後執行資料流程活動以轉換資料。 例如，管道將首先複製到 Blob 存儲中，然後資料流程活動將使用源中的資料集來轉換該資料。

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>自託管的集成運行時是否可用於資料流程？

自承載 IR 是 ADF 管道構造，可用於複製活動，以獲取或移動資料，以及從基於 VM 的資料來源和接收器。 首先使用 Copy 暫行資料，然後使用資料流程進行轉換，然後進行後續複製（如果需要將轉換的資料移回 onprem 存儲）。

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>資料流程計算引擎是否為多個租戶提供服務？
群集永遠不會共用。 我們保證在生產運行中運行的每個作業都隔離。 在調試方案的情況下，一個人得到一個群集，並且所有調試都將轉到該使用者啟動的群集。

## <a name="wrangling-data-flows"></a>旋轉資料流程

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>用於爭論資料流程的支援區域是什麼？

在以下區域創建的資料工廠中，當前支援爭鳴資料流程：

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

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>在爭論資料流程方面有哪些限制和約束？

資料集名稱只能包含字母數位字元。 支援以下資料存儲：

* 使用帳戶金鑰身份驗證在 Azure Blob 存儲中分隔文本資料集
* 使用帳戶金鑰或服務主體身份驗證在 Azure 資料湖存儲第 2 代中分隔文本資料集
* 使用服務主體身份驗證在 Azure 資料湖存儲第 1 代中分隔文本資料集
* 使用 sql 身份驗證的 Azure SQL 資料庫和資料倉儲。 請參閱下面的受支援的 SQL 類型。 沒有對資料倉儲的 PolyBase 或暫存支援。

此時，連結服務金鑰保存庫集成不支援在爭用資料流程中。

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>映射和爭論資料流程之間的區別是什麼？

映射資料流程提供了一種在不需要任何編碼的情況下大規模轉換資料的方法。 您可以通過構造一系列轉換在資料流程畫布中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 使用接收器完成資料流程，將結果放在目的地。 映射資料流程非常擅長使用接收器和源中的已知和未知架構映射和轉換資料。

通過火花執行，通過動態查詢線上混搭編輯器，通過動態資料流進行敏捷資料準備和探索。 隨著資料湖的興起，有時您只需探索資料集或在湖中創建資料集。 您沒有映射到已知目標。 爭用資料流程用於不太正式和基於模型的分析方案。

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>電源平臺資料流程和爭鳴資料流程之間的區別是什麼？

Power 平臺資料流程允許使用者將來自各種資料來源的資料導入和轉換到公共資料服務和 Azure 資料湖中，以構建 PowerApps 應用程式、Power BI 報告或流自動化。 Power 平臺資料流程使用已建立的電源查詢資料準備體驗，類似于 Power BI 和 Excel。 Power 平臺資料流程還便於在組織內重用，並自動處理業務流程（例如，在刷新前一個資料流程時自動刷新依賴于另一個資料流程的資料流程）。

Azure 資料工廠 （ADF） 是一個託管資料整合服務，允許資料工程師和公民資料整合商創建複雜的混合資料轉換載入 （ETL） 和資料轉換 （ELT） 工作流。 ADF 中Wrangrang）資料流程為使用者提供了無代碼、無伺服器環境，可簡化雲中的資料準備，並擴展到任何資料大小，無需進行基礎架構管理。 它使用 Power 查詢資料準備技術（也用於電源平臺資料流程、Excel、Power BI）來準備和塑造資料。 為了處理大資料整合的所有複雜性和規模挑戰，爭鳴的資料流程允許使用者通過火花執行快速大規模準備資料。 使用者可以使用基於瀏覽器的介面在可訪問的可視環境中構建彈性資料管道，讓 ADF 處理 Spark 執行的複雜性。 為管道生成計畫，並從 ADF 監視門戶監視資料流程執行。 借助 ADF 豐富的可用性監視和警報，輕鬆管理資料可用性 SL，並利用內置的持續集成和部署功能在託管環境中保存和管理流。 建立警報並查看執行計畫，以驗證邏輯在調整資料流程時是否按計劃執行。

### <a name="supported-sql-types"></a>受支援的 SQL 類型

旋轉資料流程支援 SQL 中的以下資料類型。 對於使用不支援的資料類型，您將獲得驗證錯誤。

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

將來將支援其他資料類型。

## <a name="next-steps"></a>後續步驟
如需建立資料處理站的逐步指示，請參閱下列教學課程：

- [快速入門：創建資料工廠](quickstart-create-data-factory-dot-net.md)
- [教學課程：複製雲端中的資料](tutorial-copy-data-dot-net.md)
