---
title: 部署 Web 服務
titleSuffix: ML Studio (classic) - Azure
description: 如何將訓練實驗轉換成預測實驗、準備部署，然後將它部署為 Azure Machine Learning Studio （傳統） web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218136"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>部署 Azure Machine Learning Studio （傳統） web 服務

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio （傳統）可讓您建立和測試預測性分析解決方案。 您接著可以將解決方案部署為 Web 服務。

Machine Learning Studio （傳統） web 服務會在應用程式和 Machine Learning Studio （傳統）工作流程評分模型之間提供介面。 外部應用程式可以即時與 Machine Learning Studio （傳統）工作流程計分模型通訊。 對 Machine Learning Studio （傳統） web 服務的呼叫會將預測結果傳回外部應用程式。 若要進行 Web 服務呼叫，您可以傳遞在部署 Web 服務時所建立的 API 金鑰。 Machine Learning Studio （傳統） web 服務是以 REST 為基礎，這是一種熱門的 web 程式設計專案架構選擇。

Azure Machine Learning Studio （傳統）有兩種類型的 web 服務：

* 要求回應服務 (RRS)：單一資料記錄的低延遲、高擴充性服務。
* 批次執行服務 (BES)：一種非同步的服務，可為一批資料記錄進行計分。

BES 的輸入就像 RRS 使用的資料輸入。 主要差異在於，BES 會從各種來源讀取記錄區塊，例如 Azure Blob 儲存體、Azure 表格儲存體、Azure SQL Database、HDInsight (Hive 查詢) 和 HTTP 來源。

從高階觀點而言，部署模型分為下列三個步驟：

* **[建立訓練實驗]** -在 Studio （傳統）中，您可以使用一組大型內建機器學習演算法來定型和測試預測性分析模型，其方式是使用您所提供的定型資料。
* **[將其轉換為預測實驗]** - 一旦您的模型已使用現有資料訓練好，並準備好使用該模型為新資料評分之後，您就是在準備並簡化您的實驗進行預測。
* **部署**為 **[新式 Web 服務]** 或 **[傳統 Web 服務]** - 您將您的預測實驗部署為 Azure Web 服務，使用者可將資料傳送至您的模型，並收到您模型的預測。

## <a name="create-a-training-experiment"></a>建立訓練實驗

若要定型預測性分析模型，您可以使用 Azure Machine Learning Studio （傳統）來建立訓練實驗，其中包含各種模組來載入訓練資料、視需要準備資料、套用機器學習演算法，以及評估更. 您可以逐一查看實驗，並且嘗試不同的機器學習演算法以比較及評估結果。

關於建立和管理訓練實驗的處理，其他地方有更詳盡的說明。 如需詳細資訊，請參閱這些文章：

* [在 Azure Machine Learning Studio 中建立簡單的實驗（傳統）](create-experiment.md)
* [使用 Azure Machine Learning Studio 開發預測性解決方案（傳統）](tutorial-part1-credit-risk.md)
* [將定型資料匯入 Azure Machine Learning Studio （傳統）](import-data.md)
* [管理 Azure Machine Learning Studio 中的實驗反復專案（傳統）](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>將訓練實驗轉換為預測實驗

完成模型訓練後，您就可以將訓練實驗轉換成預測實驗，給新資料評分。

轉換為預測實驗之後，您就準備好定型模型，可以當做評分 Web 服務部署。 Web 服務的使用者可以將輸入資料傳送到您的模型，然後您的模型就會傳回預測結果。 當您轉換為預測實驗時，必須記住您預期其他人會如何使用您的模型。

若要將您的訓練實驗轉換為預測實驗，請按一下實驗畫布底端的 [執行]，按一下 [設定 Web 服務]，然後選取 [預測 Web 服務]。

![轉換為評分實驗](./media/publish-a-machine-learning-web-service/figure-1.png)

如需如何執行這項轉換的詳細資訊，請參閱[如何在 Azure Machine Learning Studio （傳統）中準備您的模型以進行部署](convert-training-experiment-to-scoring-experiment.md)。

下列步驟說明將預測實驗部署為新式 Web 服務。 您也可以將實驗部署為傳統 Web 服務。

## <a name="deploy-it-as-a-new-web-service"></a>部署為新式 Web 服務

既然已經備妥預測實驗，您現在即可將它部署為新式 (Resource Manager 型) Azure Web 服務。 使用者可以使用 Web 服務，將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測性實驗，請按一下實驗畫布底端的 [執行] 。 實驗完成執行之後，請按一下 [部署 Web 服務]，然後選取 [部署 Web 服務[新式]]。  [Machine Learning Studio （傳統） Web 服務入口網站] 的 [部署] 頁面隨即開啟。

> [!NOTE] 
> 若要部署新的 Web 服務，您必須在要部署 Web 服務的訂用帳戶中具備足夠的權限。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。 

### <a name="web-service-portal-deploy-experiment-page"></a>Web 服務入口網站的 [部署實驗] 頁面

在 [部署實驗] 頁面上，輸入 Web 服務的名稱。
選取定價方案。 如果您有現有的定價方案，可以進行選取，否則您必須為服務建立新的定價方案。

1. 在 [價格方案] 下拉式清單中，選取現有的方案或選取 [選取新的方案] 選項。
2. 在 [方案名稱]中，輸入將識別您帳單上方案的名稱。
3. 選取其中一個 [每月方案層]。 方案層預設為您預設區域的方案，而您的 Web 服務會部署到該區域。

按一下 [部署]，Web 服務的 [快速入門] 頁面就會開啟。

Web 服務的 [快速入門] 頁面可讓您存取建立 Web 服務之後最常執行的工作，並提供指引。 從這裡您可以輕鬆地存取 [測試] 頁面和 [取用] 頁面。

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>測試新式 Web 服務

若要測試新的 Web 服務，請在常見工作下按一下 [測試 Web 服務] 。 在 [測試] 頁面上，您可以將 Web 服務當成要求-回應服務 (RRS) 或批次執行服務 (BES) 來測試。

RRS 測試頁面會顯示輸入、輸出以及任何您已為實驗定義的全域參數。 若要測試 Web 服務，您可以手動輸入適當的值作為輸入，或提供包含測試值的逗號分隔值 (CSV) 格式檔案。

若要使用 RRS 測試，請從清單檢視模式，針對輸入輸入適當的值，並按一下 [測試要求-回應]。 您的預測結果會顯示在左邊的輸出資料行。

![輸入適當的值來測試您的 web 服務](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要測試您的 BES，請按一下 [批次]。 在 [批次] 測試頁面上，在您的輸入下按一下 [瀏覽] 並選取包含適當範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且已使用 Machine Learning Studio （傳統）建立預測實驗，您可以下載預測性實驗的資料集並加以使用。

若要下載資料集，請開啟 [Machine Learning Studio （傳統）]。 開啟您的預測實驗，並以滑鼠右鍵按一下實驗的輸入。 從操作功能表中，選取 [資料集]，然後選取 [下載]。

![從 Studio （傳統）畫布下載您的資料集](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

按一下 [ **測試**]。 批次執行作業的狀態會顯示在右邊的 [測試批次作業] 之下。

![使用 web 服務入口網站測試您的批次執行作業](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

在 [組態] 頁面上，可以變更 Web 服務的描述、標題、更新儲存體帳戶金鑰，以及啟用範例資料。

![設定您的 web 服務](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>存取新式 Web 服務

從 Machine Learning Studio （傳統）部署 web 服務之後，您可以將資料傳送至服務，並以程式設計方式接收回應。

[取用] 頁面提供您存取 Web 服務所需的所有資訊。 例如，API 金鑰可用來允許經過授權的存取服務。

如需存取 Machine Learning Studio （傳統） web 服務的詳細資訊，請參閱[如何使用 Azure Machine Learning Studio （傳統） web 服務](consume-web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新式 Web 服務

您可以使用 Machine Learning Studio （傳統） Web 服務入口網站來管理新的 web 服務。 從[入口網站主頁面](https://services.azureml.net/)按一下 [Web 服務]。 從 Web 服務頁面可以刪除或複製服務。 若要監視特定的服務，請按一下服務，然後按一下 [儀表板]。 若要監視與 Web 服務相關聯的批次作業，請按一下 [批次要求記錄檔]。

### <a id="multi-region"></a> 將您的新式 Web 服務部署到多個區域

您很容易就可以將 Web 服務部署到多個區域，而不需要多個訂用帳戶或工作區。

價格依照區域而有所不同，因此您需要為您要部署 Web 服務的每個區域定義計費方案。

#### <a name="create-a-plan-in-another-region"></a>在其他區域中建立方案

1. 登入 [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)。
2. 按一下 [方案] 功能表選項。
3. 在方案概觀頁面上，按一下 [新增]。
4. 從 [訂用帳戶] 下拉式清單中，選取新方案所在的訂用帳戶。
5. 從 [區域] 下拉式清單中，選取新方案的區域。 所選區域的「方案選項」會顯示在頁面的 [方案選項] 區段中。
6. 從 [資源群組] 下拉式清單中，選取方案的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
7. 在 [方案名稱] 中，輸入方案的名稱。
8. 在 [方案選項]下，按一下新方案的計費層級。
9. 按一下頁面底部的 [新增]。

#### <a name="deploy-the-web-service-to-another-region"></a>將 Web 服務部署到另一個區域

1. 在 Microsoft Azure Machine Learning Web 服務頁面上，按一下 [Web 服務] 功能表選項。
2. 選取您要部署到新區域的 Web 服務。
3. 按一下 [複製]。
4. 在 [Web 服務名稱]中，輸入 Web 服務的新名稱。
5. 在 [Web 服務描述]中，輸入 Web 服務的描述。
6. 從 [訂用帳戶] 下拉式清單中，選取新 Web 服務所在的訂用帳戶。
7. 從 [資源群組] 下拉式清單中，選取 Web 服務的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
8. 從 [區域] 下拉式清單中，選取要部署 Web 服務的區域。
9. 從 [儲存體帳戶] 下拉式清單中，選取要儲存 Web 服務的儲存體帳戶。
10. 從 [價格方案] 下拉式清單中，在您在步驟 8 選取的區域中選取方案。
11. 按一下 [複製]。

## <a name="deploy-it-as-a-classic-web-service"></a>部署為傳統 Web 服務

既然已經充分備妥預測實驗，您現在便可將它部署為「傳統」Azure Web 服務。 使用者可以使用 Web 服務，將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測實驗，請按一下實驗畫布底端的 [執行]，然後按一下 [部署 Web 服務]。 系統會設定 Web 服務，且會將您帶往 Web 服務儀表板。

![從 Studio 部署您的 web 服務（傳統）](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>測試傳統 Web 服務

您可以在 Machine Learning Studio （傳統） Web 服務入口網站或 Machine Learning Studio （傳統）中測試 web 服務。

若要測試「要求-回應」Web 服務，請按一下 Web 服務儀表板中的 [測試] 按鈕。 對話方塊隨即顯示，要求您提供服務的輸入資料。 這些是評分實驗預期的資料行。 輸入一組資料，然後按一下 [確定]。 Web 服務產生的結果會顯示在儀表板底部。

您可以按一下 [**測試**預覽] 連結，在 Azure Machine Learning Studio （傳統） web 服務入口網站中測試您的服務，如先前的「新的 web 服務」一節所示。

若要測試批次執行服務，請按一下 [測試] 預覽連結。 在 [批次] 測試頁面上，在您的輸入下按一下 [瀏覽] 並選取包含適當範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且已使用 Machine Learning Studio （傳統）建立預測實驗，您可以下載預測性實驗的資料集並加以使用。

![測試 Web 服務](./media/publish-a-machine-learning-web-service/figure-3.png)

在 [組態] 索引標籤上，您可以變更服務的顯示名稱，並且給予描述。 名稱和描述會顯示在管理 Web 服務的 [Azure 入口網站](https://portal.azure.com/) 中。

您可以為輸入資料、輸出資料及 Web 服務參數提供描述，方法是在 [輸入結構描述]、[輸出結構描述] 及 [Web 服務參數] 底下的每個資料行輸入字串。 這些說明會用於為 Web 服務提供的範例程式碼文件。

您可以啟用記錄來診斷您在 Web 服務被存取時看到的任何錯誤。 如需詳細資訊，請參閱[啟用 Machine Learning Studio （傳統） web 服務的記錄功能](web-services-logging.md)。

![在 web 服務入口網站中啟用記錄](./media/publish-a-machine-learning-web-service/figure-4.png)

您也可以在 Azure Machine Learning Web 服務入口網站中設定 Web 服務的端點，作法類似先前在「新式 Web 服務」一節所示的程序。 選項有所不同，您可以新增或變更服務描述、啟用記錄，以及啟用範例資料進行測試。

### <a name="access-your-classic-web-service"></a>存取傳統 Web 服務

從 Azure Machine Learning Studio （傳統）部署 web 服務之後，您可以將資料傳送至服務，並以程式設計方式接收回應。

儀表板提供您存取 Web 服務所需的所有資訊。 例如，提供 API 金鑰以允許服務的授權存取權，以及提供 API 說明頁面以協助您開始撰寫程式碼。

如需存取 Machine Learning Studio （傳統） web 服務的詳細資訊，請參閱[如何使用 Azure Machine Learning Studio （傳統） web 服務](consume-web-services.md)。

### <a name="manage-your-classic-web-service"></a>管理傳統 Web 服務

您可以執行各種動作來監視 Web 服務。 您可以更新它和刪除它。 除了部署傳統 Web 服務時所建立的預設端點之外，您也可以新增其他端點至傳統 Web 服務。

如需詳細資訊，請參閱[管理 Azure Machine Learning Studio （傳統）工作區](manage-workspace.md)和[使用 Azure Machine Learning Studio （傳統） web 服務入口網站管理 web 服務](manage-new-webservice.md)。

## <a name="update-the-web-service"></a>更新 Web 服務
您可以對您的 Web 服務進行變更，例如使用其他訓練資料更新模型，以及再次部署、覆寫原始 Web 服務。

若要更新 Web 服務，請開啟您用來部署 Web 服務的原始預測實驗，然後按一下 [另存新檔]以製作可編輯的複本。 進行變更，然後按一下 [部署 Web 服務]。

由於您之前部署了這項實驗，所以會詢問您要覆寫 (傳統 Web 服務) 或更新 (新的 Web 服務) 現有的服務。 按一下 [是] 或 [更新] 會停止現有的 Web 服務，然後在其位置部署新的預測實驗。

> [!NOTE]
> 如果您在原始 Web 服務中進行組態變更，例如輸入新的顯示名稱或說明，則您必須再次輸入這些值。

更新 Web 服務的一個選擇是以程式設計方式重新定型模型。 如需詳細資訊，請參閱以程式設計方式重新定型[Machine Learning Studio （傳統）模型](/azure/machine-learning/studio/retrain-machine-learning-model)。

## <a name="next-steps"></a>後續步驟

* 如需部署運作方式的更多技術詳細資料，請參閱[Machine Learning Studio （傳統）模型如何從實驗進入運作 Web 服務](model-progression-experiment-to-web-service.md)。

* 如需如何讓模型準備好部署的詳細資訊，請參閱[如何準備您的模型以在 Azure Machine Learning Studio （傳統）中進行部署](convert-training-experiment-to-scoring-experiment.md)。

* 有數種方法可以使用 REST API 和存取 Web 服務。 請參閱[如何使用 Azure Machine Learning Studio （傳統） web 服務](consume-web-services.md)。

<!-- internal links -->
[建立訓練實驗]: #create-a-training-experiment
[將其轉換為預測實驗]: #convert-the-training-experiment-to-a-predictive-experiment
[新式 Web 服務]: #deploy-it-as-a-new-web-service
[傳統 Web 服務]: #deploy-it-as-a-classic-web-service
[新式]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
