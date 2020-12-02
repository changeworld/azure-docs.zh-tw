---
title: ML Studio (傳統版) 教學課程 3：部署信用風險模型 - Azure
description: 一個詳盡的教學課程，說明如何在 Azure Machine Learning (傳統) 中建立適用於信用風險評估的預測性分析解決方案。 本教學課程是三部分教學課程系列的第三部分。 其中顯示如何將模型部署為 Web 服務。
keywords: 信用風險, 預測性分析解決方案, 風險評估, 部署, web 服務
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 3afcf87c360651ac314450910fbf5ab72afd289a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503908"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>教學課程 3：部署信用風險模型 - Azure Machine Learning Studio (傳統)

**適用於：** ![這是一個核取記號，表示本文適用於 Machine Learning Studio (傳統版)。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)   ![這是一個 X，這表示本文適用於 Azure Machine Learning。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

在本教學課程中，您將進一步了解開發預測性分析解決方案的程序。 您將在 Machine Learning Studio (傳統) 中建立簡單的模型。  接著，您會將該模型部署為 Azure Machine Learning Web 服務。  這個已部署的模型可使用新資料進行預測。 本教學課程是 **三部分教學課程系列的第三部分**。

假設您必須根據某個人在信用申請書上提供的資訊預測其信用風險。  

信用風險評估是一個複雜的問題，但本教學課程將稍微加以簡化。 您將使用它作為範例，以說明如何使用 Microsoft Azure Machine Learning Studio (傳統) 建立預測性分析解決方案。 針對此解決方案，您將使用 Azure Machine Learning Studio (傳統) 和 Machine Learning Web 服務。 

在此三部分的教學課程中，您將從可公開取得的信用風險資料開始著手。  接著，您將開發並訓練預測模型。  最後，您會將模型部署為 Web 服務。

在[本教學課程的第一部分](tutorial-part1-credit-risk.md)，您已建立 Machine Learning Studio (傳統) 工作區、上傳資料，以及建立實驗。

在[本教學課程的第二部分](tutorial-part2-credit-risk-train.md)，您已訓練及評估模型。

在教學課程的這個部分中，您將：

> [!div class="checklist"]
> * 準備開始部署
> * 部署 Web 服務
> * 測試 Web 服務
> * 管理 Web 服務
> * 存取 Web 服務

## <a name="prerequisites"></a>必要條件

完成[本教學課程的第二部分](tutorial-part2-credit-risk-train.md)。

## <a name="prepare-for-deployment"></a>準備開始部署
為了讓其他人有機會使用您在本教學課程中開發的預測模型，您可以將它部署為 Azure 上的 Web 服務。

截至目前為止，您一直在試驗如何定型模型。 但是已部署的服務就不會再進行訓練，它會根據我們的模型，對使用者的輸入計分以產生預測。 所以我們要進行一些準備工作，將這項實驗從***訓練** _實驗轉換為預測實驗。 

部署準備是包含三個步驟的程序：  

1. 移除其中一個模型
1. 將您所建立的_訓練實驗*轉換成「預測實驗」
1. 將預測實驗部署為 Web 服務

### <a name="remove-one-of-the-models"></a>移除其中一個模型

首先，您需要更精簡這項實驗。 您目前在實驗中有兩個不同的模型，但將此實驗部署為 Web 服務時，您只要一個模型。  

假設您已判斷出促進式樹狀模型比 SVM 模型更適合。 因此，首要之務是移除[二元支援向量機器][two-class-support-vector-machine]模組，以及用於訓練該模組的其他模組。 您可能需要按一下實驗畫布底部的 [ **另存新檔** ]，先建立一份實驗複本。

您必須刪除下列模組：  

* [二元支援向量機器][two-class-support-vector-machine]
* 與其連線的[訓練模型][train-model]和[評分模型][score-model]模組
* [標準化資料][normalize-data] (兩者)
* [評估模型][evaluate-model] (因為我們已完成模型的評估)

選取每個模組然後按 Delete 鍵，或用右鍵按一下模組並選取 [刪除]。 

![醒目提示要刪除的模組以移除支援向量機器模型](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

我們的模型現在看起來應該像這樣：

![刪除支援向量機器模型後的實驗結果](./media/tutorial-part3-credit-risk-deploy/publish3.png)

現在我們已經準備好使用[二元促進式決策樹][two-class-boosted-decision-tree]來部署此模型。

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>將訓練實驗轉換為預測實驗

若要備妥此模型以進行部署，您需要將此訓練實驗轉換為預測實驗。 這牽涉到三個步驟：

1. 儲存已定型的模型，並取代您的定型模組
1. 精簡實驗，移除只有定型才需要的模組
1. 定義 Web 服務將接受輸入的位置和產生輸出的位置

您可以手動進行此動作，但幸好上述三個步驟只要按一下實驗畫布底端的 [設定 Web 服務] (然後選取 [預測性 Web 服務] 選項) 即可完成。

> [!TIP]
> 如需將訓練實驗轉換成預測實驗的詳細資訊，請參閱[如何準備您的模型以在 Azure Machine Learning Studio (傳統) 中部署](deploy-a-machine-learning-web-service.md)。

當您按一下 [設定 Web 服務] ，會發生幾件事：

* 定型的模型會轉換為單一 **定型模型** 模組，並存放在實驗畫布左側的模組選擇區中 (您可以在 [定型模型] 下找到這個模組)
* 用於定型的模組已遭到移除，尤其是：
  * [二元促進式決策樹][two-class-boosted-decision-tree]
  * [訓練模型][train-model]
  * [資料分割][split]
  * 用於測試資料的第二個[執行 R 指令碼][execute-r-script]模組
* 儲存的定型模型會加回實驗中
* 會新增 **Web 服務輸入** 和 **Web 服務輸出** 模組 (這些可用來辨識使用者的資料將在何處輸入模型中、傳回什麼資料、在何時存取 Web 服務)

> [!NOTE]
> 您可以看到，實驗已儲存在已新增至實驗畫布頂端之索引標籤下的兩個部分。 原始的訓練實驗位於 [訓練實驗] 索引標籤底下，新建立的預測實驗位於 [預測實驗] 底下。 您將預測實驗部署為 Web 服務。

您需要對這項特別的實驗採取一個額外步驟。
您新增了兩個[執行 R 指令碼][execute-r-script]模組來替資料提供加權函式。 這只是您為了訓練和測試所用的技巧，因此您可以從最終模型中拿掉這些模組。
Machine Learning Studio (傳統) 已在移除[分割][split]模組時移除一個[執行 R 指令碼][execute-r-script]模組。 您現在可以移除另一個模組，並直接將[中繼資料編輯器][metadata-editor]連線到[評分模型][score-model]。    

實驗現在看起來如下：  

![Scoring the trained model](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> 您可能驚訝您為什麼要在預測實驗中留下「UCI 德國信用卡資料」資料集。 服務即將評分使用者的資料，而不是原始資料集，所以為何要讓原始資料集留在模型中？
> 
> 服務的確不需要原始信用卡資料。 但卻需要該資料的結構描述，例如，有多少個資料行及哪些資料行是數值等資訊。 需要此結構描述資訊才能解譯使用者的資料。 您保持連接這些元件，以便服務執行時，評分模型才會有資料集結構描述。 不會使用資料，只是使用結構描述。  
> 
>一個值得注意的重要事項是若您的原始資料集包含標籤，則來自 Web 輸入的預期結構描述也預期應該有具有標籤的資料行！ 避免此問題的一個因應措施是移除標籤，以及在將 Web 輸入與定型資料集連結到通用模組之前，移除位於定型資料集但將不會位於 Web 輸入中的任何其他資料。 
> 

最後一次執行實驗 (按一下 **[執行]** )。如果要確認模型仍然有效，請按一下 [評分模型][score-model]模組的輸出結果，並選取 [檢視結果]。 您可以看到原始資料出現，也會看到信用風險值 ("Scored Labels") 和評分機率值 ("Scored Probabilities")。 

## <a name="deploy-the-web-service"></a>部署 Web 服務
您可以將實驗部署為傳統 Web 服務或架構在 Azure Resource Manager 上的新式 Web 服務。

### <a name="deploy-as-a-classic-web-service"></a>部署為傳統 Web 服務
若要部署衍生自實驗的傳統 Web 服務，請按一下畫布下方的 [部署 Web 服務]，然後選取 [部署 Web 服務 [傳統]]。 Machine Learning Studio (傳統) 會將實驗當做 Web 服務部署，並帶您前往 Web 服務的儀表板。 您可以從此頁面返回實驗 (**檢視快照** 或 **檢視最新**)，並執行簡單的 Web 服務測試 (請參閱下面的 **測試 Web 服務**)。 這裡還有建立應用程式以存取 Web 服務的資訊 (此教學課程的下一個步驟提供這方面的詳細資料)。

![Web 服務儀表板](./media/tutorial-part3-credit-risk-deploy/publish6.png)


您可以按一下 **設定**  索引標籤來設定服務。您可以在這裡修改服務名稱 (預設會指定實驗名稱) 並輸入描述。 您也可以為輸入和輸出資料指定更好記的標籤。  

![設定 Web 服務](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>部署為新式 Web 服務

> [!NOTE] 
> 若要部署新式 Web 服務，您必須在要部署 Web 服務的訂用帳戶中具備足夠的權限。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。 

部署衍生自實驗的新式 Web 服務：

1. 按一下畫布底下的 **部署 Web 服務**，然後選取 **部署 Web 服務 [新式]** 。 Machine Learning Studio (傳統) 會帶您到 Azure Machine Learning Web 服務的 [部署實驗] 頁面。

1. 輸入 Web 服務的名稱。 

1. 在 **價格方案**，您可以選取現有的定價方案，或選取 [建立新的] 並指定新的方案的名稱，然後選取每月方案選項。 方案層預設為您預設區域的方案，而您的 Web 服務會部署到該區域。

1. 按一下 [ **部署**]。

幾分鐘後，Web 服務的 **快速入門** 頁面就會開啟。

您可以按一下 [設定] 索引標籤來設定服務。您可以在這裡修改服務標題並輸入描述。 

若要測試 Web 服務，按一下 [測試] 索引標籤 (請參閱下面的 **測試 Web 服務**)。 如需建立應用程式以存取 Web 服務的相關資訊，請按一下 [取用] 索引標籤 (此教學課程的下一個步驟將提供更多詳細資料)。

> [!TIP]
> 您可以在部署 Web 服務之後進行更新。 例如，如果想要變更模型，則可以編輯訓練實驗、調整模型參數，然後按一下 [部署 Web 服務]，選取 [部署 Web 服務 [傳統]] 或 [部署 Web 服務 [新式]]。 重新部署實驗時，將會取代 Web 服務 (現在使用的是已更新的模型)。  
> 
> 

## <a name="test-the-web-service"></a>測試 Web 服務

存取 Web 服務時，使用者的資料會透過 **Web 服務輸入** 模組輸入，再透過它傳遞給 [評分模型][score-model]模組進行評分。 基於您設定預測實驗的方式，模型預期會得到與原始信用風險資料集相同格式的資料。
接著透過 **Web 服務輸出** 模組，將結果從 Web 服務傳回給使用者。

> [!TIP]
> 基於您設定預測實驗的方式，會傳回[評分模型][score-model]模組的整個結果。 這包括所有輸入的資料以及信用風險值和評分機率。 但您可以視需要傳回不同的項目，例如，您可以只傳回信用風險值。 若要這樣做，請在 [評分模型][score-model]和 **Web 服務輸出** 之間插入 [選取資料行][select-columns]模組，以排除您不想讓 Web 服務傳回的資料行。 
> 
> 

您可以在 **Machine Learning Studio (傳統)** 或 **Azure Machine Learning Web 服務** 入口網站中測試傳統 Web 服務。
您只能在 **Machine Learning Web 服務** 入口網站中測試新式 Web 服務。

> [!TIP]
> 在 Azure Machine Learning Web 服務入口網站中測試時，您可以用入口網站建立範例資料以用於測試要求-回應服務。 在 [設定] 頁面上，[啟用範例資料嗎?] 選取 [是]。 當您開啟 [測試] 頁面上的 [要求-回應] 索引標籤時，入口網站會填入取自原始信用風險資料集的範例資料。

### <a name="test-a-classic-web-service"></a>測試傳統 Web 服務

您可以在 Machine Learning Studio (傳統) 或 Machine Learning Web 服務入口網站中測試傳統 Web 服務。 

#### <a name="test-in-machine-learning-studio-classic"></a>在 Machine Learning Studio (傳統) 中測試

1. 在 Web 服務的 [儀表板] 頁面上，按一下 [預設端點] 下的 [測試] 按鈕。 對話方塊隨即顯示，要求您提供服務的輸入資料。 這些就是在原始的信用風險資料集中出現的資料行。  

1. 輸入一組資料，然後按一下 [確定] 。 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>在 Machine Learning Web 服務入口網站中測試

1. 在 Web 服務的 [儀表板] 頁面上，按一下 [預設端點] 下的 [測試預覽] 連結。 Azure Machine Learning Web 服務入口網站中的 Web 服務端點測試頁面隨即開啟，並要求您提供服務的輸入資料。 這些就是在原始的信用風險資料集中出現的資料行。

2. 按一下 [測試要求-回應]。 

### <a name="test-a-new-web-service"></a>測試新式 Web 服務

您只能在 Machine Learning Web 服務入口網站中測試新式 Web 服務。

1. 在 [Azure Machine Learning Web 服務](https://services.azureml.net/quickstart)入口網站中，按一下頁面頂端的 [測試]。 [測試] 頁面會開啟，您可以輸入服務的資料。 顯示的輸入欄位會對應到在原始的信用風險資料集中出現的資料行。 

1. 輸入一組資料，然後按一下 [測試要求-回應] 。

測試的結果會顯示在頁面右邊的輸出資料行上。 


## <a name="manage-the-web-service"></a>管理 Web 服務

一旦部署 Web 服務 (傳統或新式) 之後，您就可以從 [Microsoft Azure Machine Learning Web 服務](https://services.azureml.net/quickstart)入口網站管理它。

監視 Web 服務的效能：

1. 登入 [Microsoft Azure Machine Learning Web 服務](https://services.azureml.net/quickstart)入口網站
1. 按一下 [Web 服務]
1. 按一下您的 Web 服務
1. 按一下 [儀表板]

## <a name="access-the-web-service"></a>存取 Web 服務

在此教學課程先前的步驟中，您已經部署一個使用我們的信用風險預測模型的 Web 服務。 現在使用者能夠將資料傳送至服務並接收結果。 

此 Web 服務是可使用 REST API，透過下列其中一種方式接收和傳回資料的 Azure Web 服務：  

* **要求/回應** - 使用者以 HTTP 通訊協定，將一或多列的信用資料傳送給服務，然後服務回應一或多組結果。
* **批次執行** - 使用者在 Azure Blob 中儲存一或多列信用資料，然後將 Blob 位置傳送給服務。 服務會給輸入 Blob 中的所有資料列評分，將結果儲存在另一個 Blob 中，再傳回該容器的 URL。 

> [!NOTE]
> Studio (傳統版) 中的功能資料行名稱會 **區分大小寫**。 請確定您用來叫用 Web 服務的輸入資料具有與訓練資料集相同的資料行名稱。

如需存取和使用 Web 服務的詳細資訊，請參閱[使用 Azure Machine Learning Web 服務與 Web 應用程式範本](./consume-web-services.md)。



## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列步驟：

> [!div class="checklist"]
> * 準備開始部署
> * 部署 Web 服務
> * 測試 Web 服務
> * 管理 Web 服務
> * 存取 Web 服務

您也可以使用以 R、C# 和 Python 程式語言為您提供的起始程式碼來開發自訂應用程式以存取 Web 服務。

> [!div class="nextstepaction"]
> [使用 Azure Machine Learning Web 服務](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset