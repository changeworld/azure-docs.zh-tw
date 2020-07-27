---
title: 教學課程：建立影像分類的標記專案
titleSuffix: Azure Machine Learning
description: 了解如何管理標記影像的程序，使其可用於多類別的影像分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.openlocfilehash: 1665bc42578c189ed27208eb31cc4bdb2e27bbb9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536159"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>教學課程：建立多類別影像分類的標記專案 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教學課程說明如何管理標記 (labeling/tagging) 影像的程序，而這些影像將作為建立機器學習模型的資料。 Azure Machine Learning 中的資料標記處於公開預覽狀態。

如果您想要訓練機器學習模型來分類影像，您需要有數百個或甚至數千個正確標記的影像。  Azure Machine Learning 可協助您管理私人領域專家小組為您的資料加上標籤時的進度。
 
在本教學課程中，您將使用貓和狗的影像。  因為每個影像不是貓就是狗，所以這是「多類別」標記專案。 您將學習如何：

> [!div class="checklist"]
>
> * 建立 Azure 儲存體帳戶並將影像上傳至帳戶。
> * 建立 Azure Machine Learning 工作區。
> * 建立多類別的影像標記專案。
> * 為您的資料加上標籤。  您或您的標記人員可以執行這項工作。
> * 藉由檢閱和匯出資料來完成專案。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>啟動標記專案

接著您會在 Azure Machine Learning Studio 中管理資料標記專案，Azure Machine Learning Studio 是一種整合介面，可為所有技能等級的資料科學從業人員，提供用以執行資料科學案例的機器學習工具。 Internet Explorer 瀏覽器不支援 Studio。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 選取訂用帳戶與您建立的工作區。

### <a name="create-a-datastore"></a><a name="create-datastore"></a>建立資料存放區

Azure Machine Learning 資料存放區用來儲存連線資訊，例如您的訂用帳戶識別碼和權杖授權。 在此，您會使用資料存放區來連線到儲存體帳戶，其中包含本教學課程的影像。

1. 在工作區的左側，選取 [資料存放區]。

1. 選取 [+ 新增資料存放區]。

1. 使用下列設定填寫表單：

    欄位|描述 
    ---|---
    資料存放區名稱 | 提供資料存放區的名稱。  我們在此使用 **labeling_tutorial**。
    資料存放區類型 | 選取儲存體的類型。  我們還會使用 **Azure Blob 儲存體**，這是影像慣用的儲存體。
    帳戶選取方法 | 選取 [手動輸入]。
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    驗證類型 | 選取 [SAS 權杖]。
    帳戶金鑰 | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. 選取 [建立] 以建立資料存放區。

### <a name="create-a-labeling-project"></a>建立加上標籤專案

您現在已可存取您想要標記的資料，接下來請建立您的標記專案。

1. 在頁面頂端選取 [專案]。

1. 選取 [+ 新增專案]。

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="建立專案":::

### <a name="project-details"></a>專案詳細資料

1. 針對 [專案詳細資料] 表單使用下列輸入：

    欄位|描述 
    ---|---
    專案名稱 | 為專案命名。  在此我們將使用 **tutorial-cats-n-dogs**。
    標記工作類型 | 選取 [影像分類多類別]。  
    
    選取 [下一步] 以繼續建立專案。

### <a name="select-or-create-a-dataset"></a>選取或建立資料集

1.   在 [選取或建立資料集] 表單上，選取第二個選擇 [建立資料集]，然後選取 [從資料存放區] 連結。

1. 針對 [從資料存放區建立資料集] 表單，使用下列輸入：

    1. 在 [基本資訊] 表單上，新增名稱，我們在此使用 **images-for-tutorial**。  視需要新增說明。  然後選取 [下一步]。
    1. 在 [資料存放區選取] 表單上，使用下拉式清單來選取 [先前建立的資料存放區]，例如 **tutorial_images (Azure Blob 儲存體)**
    1. 接下來，仍然在 [資料存放區選取] 表單上，選取 [瀏覽]，然後選取 [MultiClass - DogsCats]。  選取 [儲存] 以使用 **/MultiClass - DogsCats** 作為路徑。
    1. 選取 [下一步] 以確認詳細資料，然後選取 [建立] 來建立資料集。
    1. 選取清單中資料集名稱旁邊的圓形，例如 **images-for-tutorial**。

1. 選取 [下一步] 以繼續建立專案。

### <a name="incremental-refresh"></a>累加式重新整理

若計劃在您的資料集中新增影像，增量累加式重新整理將會尋找這些新影像，然後將其新增至您的專案。  如有啟用此功能，專案將會定期檢查有無新的影像。  在本教學課程中，您將不會新增影像 到資料存放區，所以請勿核取此功能。

選取 [下一步] 以繼續操作。

### <a name="label-classes"></a>標籤類別

1. 在 [標籤類別] 表單上，輸入標籤名稱，然後選取 [+ 新增標籤] 以輸入下一個標籤。  在此專案中，標籤為 [貓]、[狗]及 [不確定]。

1. 新增所有標籤後，選取 [下一步]。

### <a name="labeling-instructions"></a>標記指示

1. 在 [標記指示] 表單上，您可以提供網站連結，該網站會為標記人員提供詳細的指示。  我們會在本教學課程中將其保持空白。

1. 您也可以在表單上直接新增工作的簡短描述。  輸入 [標記教學課程 - 貓與狗]。

1. 選取 [下一步] 。

1. 如果您使用 Enterprise 工作區，則會看到 **ML 輔助標籤** 區段。  將核取方塊保持不核取狀態。 ML 輔助標記所需的資料比您在本教學課程中使用的還要多。

1. 選取 [建立專案]。

此頁面不會自動重新整理。 在暫停之後，手動重新整理頁面，直到專案的狀態並更為 [已建立]。

## <a name="start-labeling"></a>開始標記

您現在已設定 Azure 資源，並已設定資料標記專案。 現在可以將標籤新增至您的資料。

### <a name="tag-the-images"></a>標記影像

在本教學課程的這個部分，您的角色將會從「專案管理員」切換至「標記人員」。  具有您工作區參與者存取權的任何人都可以成為標記人員。

1. 在 [Machine Learning Studio](https://ml.azure.com) 中，選取左側的 [資料標記] 以尋找您的專案。  

1. 選取清單中的專案名稱。

1. 在專案名稱底下，選取 [標籤資料]。

1. 閱讀指示，然後選取 [工作]。

1. 選取右邊的縮圖影像，以顯示您想要一舉標記的影像數目。 您必須先標記所有影像，才能繼續進行。 只有在處理全新而未標記的資料頁面時，才可切換版面配置。 切換版面配置會清除頁面上進行中的標記工作。

1. 選取一或多個影像，然後選取要套用至選取範圍的標籤。 此標籤會出現在影像下方。  繼續選取並標記頁面上的所有影像。  若要同時選取所有顯示的影像，請選取 [全選]。 至少選取一個影像來套用標籤。


    > [!TIP]
    > 您可以使用鍵盤上的數字鍵來選取前九個標籤。

1. 標記頁面上的所有影像後，請選取 [提交] 以提交這些標籤。

    ![標記影像](media/tutorial-labeling/catsndogs.gif)

1. 在您提交手邊資料的標籤後，Azure 會以工作佇列中的一組新影像來重新整理頁面。

## <a name="complete-the-project"></a>完成專案

您現在會將角色切換回標記專案的「專案管理員」。

身為管理員的您可以檢閱標記人員的工作。  

### <a name="review-labeled-data"></a>檢閱已標記的資料

1. 在 [Machine Learning Studio](https://ml.azure.com) 中，選取左側的 [資料標記] 以尋找您的專案。  

1. 選取專案名稱連結。

1. 儀表板會顯示專案的進度。

1. 在頁面頂端選取 [資料]。

1. 在左側，選取 [已標記的資料]來查看已標記的影像。  

1. 當您不同意某個標籤時，請選取影像，然後選取頁面底部的 [拒絕]。  標記將會被移除，且影像會放回未標記的影像佇列中。

### <a name="export-labeled-data"></a>匯出已標記的資料

您可以隨時匯出 Machine Learning 實驗的標籤資料。 使用者通常會匯出多次並訓練不同的模型，而不是等待所有影像進行標記。

影像標籤可以匯出為 [COCO 格式](http://cocodataset.org/#format-data)或匯出為 Azure Machine Learning 資料集。 資料集格式可讓您輕鬆地在 Azure Machine Learning 中定型。  

1. 在 [Machine Learning Studio](https://ml.azure.com) 中，選取左側的 [資料標記] 以尋找您的專案。  

1. 選取專案名稱連結。

1. 選取 [匯出]，然後選擇 [匯出為 Azure ML 資料集]。 

    匯出的狀態會顯示在 [匯出] 按鈕正下方。 

1. 成功匯出標籤之後，請選取左側的 [資料集] 來檢視結果。

## <a name="clean-up-resources"></a>清除資源


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已標記影像。  您現在可使用已標記的資料：

> [!div class="nextstepaction"]
> [訓練機器學習影像辨識模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
