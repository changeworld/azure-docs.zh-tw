---
title: ML Studio (傳統) ：部署 web 服務-Azure
description: 如何將訓練實驗轉換為預測實驗，將它準備好進行部署，然後將它部署為 Azure Machine Learning Studio (傳統) web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: c9cfe05b6547cbdc61a1c8cc6223f08900cf09d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341845"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>部署 Azure Machine Learning Studio (傳統) web 服務

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


Azure Machine Learning Studio (傳統) 可讓您建立和測試預測性分析解決方案。 您接著可以將解決方案部署為 Web 服務。

Machine Learning Studio (傳統) web 服務會在應用程式和 Machine Learning Studio (傳統) 工作流程評分模型之間提供介面。 外部應用程式可以即時與 Machine Learning Studio (傳統) 工作流程評分模型進行通訊。 Machine Learning Studio (傳統) web 服務的呼叫會將預測結果傳回外部應用程式。 若要進行 Web 服務呼叫，您可以傳遞在部署 Web 服務時所建立的 API 金鑰。 Machine Learning Studio (傳統) web 服務是以 REST 為基礎，這是一種常見的 web 程式設計專案架構選擇。

Azure Machine Learning Studio (傳統) 有兩種類型的 web 服務：

* Request-Response 服務 (RR) ：低延遲、高度可調整的服務，可為單一資料記錄評分。
* 批次執行服務 (的) ：以一批資料記錄為分數的非同步服務。

BES 的輸入就像 RRS 使用的資料輸入。 主要差異在於，BES 會從各種來源讀取記錄區塊，例如 Azure Blob 儲存體、Azure 表格儲存體、Azure SQL Database、HDInsight (Hive 查詢) 和 HTTP 來源。

從高階觀點而言，部署模型分為下列三個步驟：

* **[建立訓練實驗]** -在 Studio (傳統) 中，您可以使用您提供的定型資料，使用一組大量的內建機器學習演算法來定型和測試預測性分析模型。
* **[將其轉換為預測實驗]** - 一旦您的模型已使用現有資料訓練好，並準備好使用該模型為新資料評分之後，您就是在準備並簡化您的實驗進行預測。
* **部署**為**[新式 Web 服務]** 或**[傳統 Web 服務]** - 您將您的預測實驗部署為 Azure Web 服務，使用者可將資料傳送至您的模型，並收到您模型的預測。

## <a name="create-a-training-experiment"></a>建立訓練實驗

若要訓練預測性分析模型，您可以使用 Azure Machine Learning Studio (傳統) 來建立訓練實驗，其中包含用來載入定型資料的各種模組、視需要準備資料、套用機器學習演算法，以及評估結果。 您可以逐一查看實驗，並且嘗試不同的機器學習演算法以比較及評估結果。

關於建立和管理訓練實驗的處理，其他地方有更詳盡的說明。 如需詳細資訊，請參閱這些文章：

* [在 Azure Machine Learning Studio (傳統) 中建立簡單的實驗 ](create-experiment.md)
* [使用 Azure Machine Learning Studio (傳統) 開發預測性解決方案 ](tutorial-part1-credit-risk.md)
* [將定型資料匯入 Azure Machine Learning Studio (傳統) ](import-data.md)
* [在 Azure Machine Learning Studio (傳統) 中管理實驗反復專案 ](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>將訓練實驗轉換為預測實驗

完成模型訓練後，您就可以將訓練實驗轉換成預測實驗，給新資料評分。

轉換為預測實驗之後，您就準備好定型模型，可以當做評分 Web 服務部署。 Web 服務的使用者可以將輸入資料傳送到您的模型，然後您的模型就會傳回預測結果。 當您轉換為預測實驗時，必須記住您預期其他人會如何使用您的模型。

將訓練實驗轉換為預測實驗包含三個步驟：

1. 將機器學習演算法模組取代為定型模型。
2. 將訓練調整為僅適用於評分所需的模組。 訓練實驗包含訓練所需但模型受過訓練之後，就不再需要的多個模組。
3. 定義模型如何接受來自 Web 服務使用者的資料，以及將傳回的資料。

> [!TIP]
> 在訓練實驗中，您曾考慮使用您自己的資料來訓練和評分模型。 但部署之後，使用者就會將新的資料傳送至您的模型，而且會傳回預測結果。 因此，將訓練實驗轉換成預測實驗以準備好進行部署時，請記住其他人如何使用模型。

![轉換為評分實驗](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>設定 Web 服務按鈕
執行實驗之後 (按一下實驗畫布底端的 [執行]****)，請按一下 [設定 Web 服務]**** 按鈕 (選取 [預測 Web 服務]**** 選項)。 [設定 Web 服務]**** 會執行三個步驟，以將訓練實驗轉換為預測實驗：

1. 它會將定型模型儲存到實驗畫布左側之模組選擇區的 [定型模型]**** 區段中。 它接著會將機器學習演算法和[定型模型][train-model]模組取代為所儲存的定型模型。
2. 它會分析您的實驗，並移除已清楚僅用於定型且不再需要的模組。
3. 它會將 _Web 服務輸入_和_輸出_模組插入實驗中的預設位置 (這些模組會接受並傳回使用者資料)。

例如，以下實驗會使用範例人口普查資料，訓練二元推進式決策樹模型：

![訓練實驗](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

這項實驗中的模組基本上執行是四個不同的功能：

![模組功能](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

當您將這個訓練實驗轉換為預測實驗時，就不再需要其中一些模組，或它們現在有不同的用途：

* **資料** - 在評分期間，不會使用此範例資料集中的資料，因為 Web 服務的使用者會提供要評分的資料。 不過，定型模型會使用此資料集中的中繼資料，例如，資料類型。 因此，您必須將資料集保留在預測實驗中，讓它能夠提供這項中繼資料。

* **準備** - 根據要提交用於評分的使用者資料而定，這些模組不一定需要處理傳入的資料。 [設定 Web 服務]**** 按鈕並未接觸這些項目，您需要決定要如何處理它們。
  
    例如，在此範例中，範例資料集可能會有遺漏的值，因此，會包含[清除遺漏資料][clean-missing-data]模型來處理它們。 而且，範例資料集會包括培訓模型不需要的資料行。 因此，包括[選取資料集中的資料行][select-columns]模組，以將那些額外的資料行從資料流程中排除。 如果您知道透過 Web 服務提交用於評分的資料不會有遺漏的值，則您可以移除[清除遺漏的資料][clean-missing-data]模組。 不過，因為[選取資料集中的資料行][select-columns]模組有助於定義定型模型所預期的資料行，所以必須保留該模組。

* **訓練** - 這些模組可用來訓練模型。 當您按一下 [設定 Web 服務]**** 時，這些模組都會取代為包含定型模型的單一模組。 這個新模組會儲存在模組調色盤的 [定型模型]**** 區段。

* **評分** - 在此範例中，[分割資料][split]模組是用來將資料流分割成測試資料和訓練資料。 在預測實驗中，我們不會再進行訓練，因此可以移除[分割資料][split]。 同樣地，第二個[評分模型][score-model]模組和[評估模型][evaluate-model]模組會用來比較測試資料的結果，因此在預測實驗中不需要這些模組。 但其餘的[評分模型][score-model]模組需要透過 Web 服務傳回評分結果。

以下是按一下 [設定 Web 服務] **** 之後，我們的範例外觀：

![已轉換的預測實驗](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

[設定 Web 服務]**** 所完成的工作可能足以準備您的實驗，以當做 Web 服務部署。 不過，您可能會想要執行一些您的實驗專屬的額外工作。

#### <a name="adjust-input-and-output-modules"></a>調整輸入和輸出模組
在訓練實驗中，您使用一組訓練資料進行一些處理，以取得 Machine Learning 演算法所需格式的資料。 如果您預期透過 Web 服務收到的資料不需要這項處理，則可以略過它︰將 **Web 服務輸入模組**的輸出連線至實驗中的不同模組。 使用者的資料現在會送到模型的這個位置。

例如，[設定 Web 服務]**** 預設會將 [Web 服務輸入]**** 模組放在資料流程的頂端，如上圖所示。 但我們可以手動將 **Web 服務輸入**放在資料處理模組之後：

![移動 Web 服務輸入](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

透過 Web 服務提供的輸入資料現在會直接傳入 [評分模型] 模組，而不需要任何前置處理。

同樣地， **設定 Web 服務** 會將 Web 服務輸出模組放在資料流程的底部。 在此範例中，Web 服務會將[評分模型][score-model]模組的輸出傳回給使用者，其中包括完整的輸入資料向量以及評分結果。
不過，如果您偏好傳回其他內容，則可以在 **Web 服務輸出**模組之前新增其他模組。 

例如，若只要傳回評分結果，而不傳回整個輸入資料向量，請新增[選取資料集中的資料行][select-columns]模組，以排除評分結果以外的所有資料行。 接著，將 **Web 服務輸出**模組移到[選取資料集中的資料行][select-columns]模組的輸出。 實驗看起來如下：

![移動 Web 服務輸出](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>新增或移除其他資料處理模組
如果在您的實驗中還有您知道在評分期間不需要的模組，則可以移除這些模組。 例如，由於我們已經將 **Web 服務輸入**模組移到資料處理模組之後的某個點，因此我們可以從預測實驗中移除[清除遺漏的資料][clean-missing-data]模組。

我們的預測實驗現在看起來像這樣：

![移除額外的模組](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>新增選用的 Web 服務參數
在某些情況下，您可能需要讓 Web 服務的使用者變更存取服務時的模組行為。 *Web 服務參數* 可讓您執行這項操作。

常見的範例是設定[匯入資料][import-data]模組，讓已部署之 Web 服務的使用者可以在存取 Web 服務時，指定不同的資料來源。 或者，設定[匯出資料][export-data]模組，以便能夠指定不同的目的地。

您可以定義 Web 服務參數，並使其與一個或多個模組參數產生關聯，而且您可以指定它們是必要還是選用參數。 Web 服務的使用者會在服務受到存取時提供這些參數的值，並據此修改模組動作。

如需 Web 服務參數和其用法的詳細資訊，請參閱[使用 Azure Machine Learning Web 服務參數][webserviceparameters]。

下列步驟說明將預測實驗部署為新式 Web 服務。 您也可以將實驗部署為傳統 Web 服務。

## <a name="deploy-it-as-a-new-web-service"></a>部署為新式 Web 服務

既然已經備妥預測實驗，您現在即可將它部署為新式 (Resource Manager 型) Azure Web 服務。 使用者可以使用 Web 服務，將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測性實驗，請按一下實驗畫布底端的 [執行] **** 。 實驗完成執行之後，請按一下 [部署 Web 服務]****，然後選取 [部署 Web 服務[新式]]****。  Machine Learning Studio (傳統) Web 服務入口網站的 [部署] 頁面隨即開啟。

> [!NOTE] 
> 若要部署新的 Web 服務，您必須在要部署 Web 服務的訂用帳戶中具備足夠的權限。 如需詳細資訊，請參閱 [使用 Azure Machine Learning Web 服務入口網站管理 web 服務](manage-new-webservice.md)。 

### <a name="web-service-portal-deploy-experiment-page"></a>Web 服務入口網站 [部署實驗] 頁面

在 [部署實驗] 頁面上，輸入 Web 服務的名稱。
選取定價方案。 如果您有現有的定價方案，可以進行選取，否則您必須為服務建立新的定價方案。

1. 在 [價格方案]**** 下拉式清單中，選取現有的方案或選取 [選取新的方案]**** 選項。
2. 在 [方案名稱] **** 中，輸入將識別您帳單上方案的名稱。
3. 選取其中一個 [每月方案層] ****。 方案層預設為您預設區域的方案，而您的 Web 服務會部署到該區域。

按一下 [ **部署** ]，web 服務的 [ **快速入門** ] 頁面隨即開啟。

Web 服務的 [快速入門] 頁面可讓您存取建立 Web 服務之後最常執行的工作，並提供指引。 從這裡您可以輕鬆地存取 [測試] 頁面和 [取用] 頁面。

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>測試新式 Web 服務

若要測試新的 web 服務，請按一下 [一般工作] 底下的 [ **測試 web 服務** ]。 在 [測試] 頁面上，您可以將 Web 服務當成要求-回應服務 (RRS) 或批次執行服務 (BES) 來測試。

RRS 測試頁面會顯示輸入、輸出以及任何您已為實驗定義的全域參數。 若要測試 Web 服務，您可以手動輸入適當的值作為輸入，或提供包含測試值的逗號分隔值 (CSV) 格式檔案。

若要使用 RRS 測試，請從清單檢視模式，針對輸入輸入適當的值，並按一下 [測試要求-回應] ****。 您的預測結果會顯示在左邊的輸出資料行。

![輸入適當的值以測試您的 web 服務](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要測試您的 BES，請按一下 [批次] ****。 在 [批次] 測試頁面上，在您的輸入下按一下 [瀏覽] 並選取包含適當範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且已使用 Machine Learning Studio (傳統) 建立預測實驗，您可以下載預測實驗的資料集，並加以使用。

若要下載資料集，請開啟 Machine Learning Studio (傳統) 。 開啟您的預測實驗，並以滑鼠右鍵按一下實驗的輸入。 從操作功能表中，選取 [資料集]****，然後選取 [下載]****。

![從 Studio (傳統) 畫布下載您的資料集](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

按一下 [ **測試**]。 批次執行作業的狀態會顯示在右邊的 [測試批次作業]**** 之下。

![使用 web 服務入口網站測試批次執行作業](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

**在 [設定**] 頁面上，您可以變更描述、標題、更新儲存體帳戶金鑰，以及啟用 web 服務的範例資料。

![設定您的 web 服務](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>存取新式 Web 服務

從 Machine Learning Studio (傳統) 部署 web 服務之後，您就可以將資料傳送至服務，並以程式設計的方式接收回應。

[取用 **] 頁面提供** 您存取 web 服務所需的所有資訊。 例如，API 金鑰可用來允許經過授權的存取服務。

如需有關存取 Machine Learning Studio (傳統) web 服務的詳細資訊，請參閱 [如何使用 Azure Machine Learning Studio (傳統) web 服務](consume-web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新式 Web 服務

您可以使用 Machine Learning Studio (傳統) Web 服務入口網站來管理新的 web 服務。 從[入口網站主頁面](https://services.azureml.net/)按一下 [Web 服務]****。 從 Web 服務頁面可以刪除或複製服務。 若要監視特定的服務，請按一下服務，然後按一下 [儀表板] ****。 若要監視與 Web 服務相關聯的批次作業，請按一下 [批次要求記錄檔] ****。

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> 將您的新式 Web 服務部署到多個區域

您很容易就可以將 Web 服務部署到多個區域，而不需要多個訂用帳戶或工作區。

價格依照區域而有所不同，因此您需要為您要部署 Web 服務的每個區域定義計費方案。

#### <a name="create-a-plan-in-another-region"></a>在其他區域中建立方案

1. 登入 [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)。
2. 按一下 [方案] **** 功能表選項。
3. 在方案概觀頁面上，按一下 [新增] ****。
4. 從 [訂用帳戶] **** 下拉式清單中，選取新方案所在的訂用帳戶。
5. 從 [區域] **** 下拉式清單中，選取新方案的區域。 所選區域的「方案選項」會顯示在頁面的 [方案選項] **** 區段中。
6. 從 [資源群組] **** 下拉式清單中，選取方案的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
7. 在 [方案名稱] **** 中，輸入方案的名稱。
8. 在 [方案選項] **** 下，按一下新方案的計費層級。
9. 按一下 [建立]。

#### <a name="deploy-the-web-service-to-another-region"></a>將 Web 服務部署到另一個區域

1. 在 Microsoft Azure Machine Learning Web 服務頁面上，按一下 [Web 服務]**** 功能表選項。
2. 選取您要部署到新區域的 Web 服務。
3. 按一下 [複製]****。
4. 在 [Web 服務名稱] **** 中，輸入 Web 服務的新名稱。
5. 在 [Web 服務描述] **** 中，輸入 Web 服務的描述。
6. 從 [訂用帳戶] **** 下拉式清單中，選取新 Web 服務所在的訂用帳戶。
7. 從 [資源群組] **** 下拉式清單中，選取 Web 服務的資源群組。 如需資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
8. 從 [區域] **** 下拉式清單中，選取要部署 Web 服務的區域。
9. 從 [儲存體帳戶] **** 下拉式清單中，選取要儲存 Web 服務的儲存體帳戶。
10. 從 [價格方案] **** 下拉式清單中，在您在步驟 8 選取的區域中選取方案。
11. 按一下 [複製]****。

## <a name="deploy-it-as-a-classic-web-service"></a>部署為傳統 Web 服務

既然已經充分備妥預測實驗，您現在便可將它部署為「傳統」Azure Web 服務。 使用者可以使用 Web 服務，將料傳送到您的模型，模型就會傳回其預測。

若要部署您的預測實驗，請按一下實驗畫布底端的 [執行]****，然後按一下 [部署 Web 服務]****。 系統會設定 Web 服務，且會將您帶往 Web 服務儀表板。

![從 Studio (傳統) 部署您的 web 服務](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>測試傳統 Web 服務

您可以在 Machine Learning Studio (傳統) Web 服務入口網站或 Machine Learning Studio (傳統) 中測試 web 服務。

若要測試「要求-回應」Web 服務，請按一下 Web 服務儀表板中的 [測試]**** 按鈕。 對話方塊隨即顯示，要求您提供服務的輸入資料。 這些是評分實驗預期的資料行。 輸入一組資料，然後按一下 [確定] 。 Web 服務產生的結果會顯示在儀表板底部。

您可以按一下 [ **測試** 預覽] 連結，在 Azure Machine Learning Studio (傳統) web 服務入口網站中測試您的服務，如先前的「新的 Web 服務」一節所示。

若要測試批次執行服務，請按一下 [測試]**** 預覽連結。 在 [批次] 測試頁面上，在您的輸入下按一下 [瀏覽] 並選取包含適當範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且已使用 Machine Learning Studio (傳統) 建立預測實驗，您可以下載預測實驗的資料集，並加以使用。

![測試 Web 服務](./media/publish-a-machine-learning-web-service/figure-3.png)

在 [組態]**** 索引標籤上，您可以變更服務的顯示名稱，並且給予描述。 名稱和描述會顯示在管理 Web 服務的 [Azure 入口網站](https://portal.azure.com/) 中。

您可以為輸入資料、輸出資料及 web 服務參數提供描述，方法是在 [ **輸入架構**]、[ **輸出架構**] 和 [ **web 服務參數**] 下的每個資料行輸入字串。 這些說明會用於為 Web 服務提供的範例程式碼文件。

您可以啟用記錄來診斷您在 Web 服務被存取時看到的任何錯誤。 如需詳細資訊，請參閱 [啟用 Machine Learning Studio (傳統) web 服務的記錄](web-services-logging.md)。

![在 web 服務入口網站中啟用記錄](./media/publish-a-machine-learning-web-service/figure-4.png)

您也可以在 Azure Machine Learning Web 服務入口網站中設定 Web 服務的端點，作法類似先前在「新式 Web 服務」一節所示的程序。 選項有所不同，您可以新增或變更服務描述、啟用記錄，以及啟用範例資料進行測試。

### <a name="access-your-classic-web-service"></a>存取傳統 Web 服務

從 Azure Machine Learning Studio (傳統) 部署 web 服務之後，您就可以將資料傳送至服務，並以程式設計的方式接收回應。

儀表板提供您存取 Web 服務所需的所有資訊。 例如，提供 API 金鑰以允許服務的授權存取權，以及提供 API 說明頁面以協助您開始撰寫程式碼。

如需有關存取 Machine Learning Studio (傳統) web 服務的詳細資訊，請參閱 [如何使用 Azure Machine Learning Studio (傳統) web 服務](consume-web-services.md)。

### <a name="manage-your-classic-web-service"></a>管理傳統 Web 服務

您可以執行各種動作來監視 Web 服務。 您可以更新它和刪除它。 除了部署傳統 Web 服務時所建立的預設端點之外，您也可以新增其他端點至傳統 Web 服務。

如需詳細資訊，請參閱[使用 Azure Machine Learning Studio (傳統) Web 服務入口](manage-new-webservice.md)網站[管理 Azure Machine Learning studio (傳統) 工作區](manage-workspace.md)和管理 web 服務。

## <a name="update-the-web-service"></a>更新 Web 服務
您可以對您的 Web 服務進行變更，例如使用其他訓練資料更新模型，以及再次部署、覆寫原始 Web 服務。

若要更新 web 服務，請開啟您用來部署 web 服務的原始預測實驗，然後按一下 [ **另存**新檔] 來製作可編輯的複本。 進行變更，然後按一下 [部署 Web 服務] ****。

由於您之前部署了這項實驗，所以會詢問您要覆寫 (傳統 Web 服務) 或更新 (新的 Web 服務) 現有的服務。 按一下 **[是]** 或 [ **更新** ] 會停止現有的 web 服務，並部署新的預測實驗。

> [!NOTE]
> 如果您在原始 Web 服務中進行組態變更，例如輸入新的顯示名稱或說明，則您必須再次輸入這些值。

更新 Web 服務的一個選擇是以程式設計方式重新定型模型。 如需詳細資訊，請參閱以程式設計方式重新定型 [Machine Learning Studio (傳統) 模型](/azure/machine-learning/studio/retrain-machine-learning-model)。

## <a name="next-steps"></a>後續步驟

* 如需部署運作方式的更多技術詳細資料，請參閱 [Machine Learning Studio (傳統) 模型如何從實驗進入實際運作 Web 服務](model-progression-experiment-to-web-service.md)。

* 如需如何準備好部署模型的詳細資訊，請參閱 [如何準備您的模型以在 Azure Machine Learning Studio (傳統) 中進行部署 ](deploy-a-machine-learning-web-service.md)。

* 有數種方法可以使用 REST API 和存取 Web 服務。 瞭解 [如何使用 Azure Machine Learning Studio (傳統) web 服務](consume-web-services.md)。

<!-- internal links -->
[建立訓練實驗]: #create-a-training-experiment
[將其轉換為預測實驗]: #convert-the-training-experiment-to-a-predictive-experiment
[新的 web 服務]: #deploy-it-as-a-new-web-service
[傳統 web 服務]: #deploy-it-as-a-classic-web-service
[新增功能]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
