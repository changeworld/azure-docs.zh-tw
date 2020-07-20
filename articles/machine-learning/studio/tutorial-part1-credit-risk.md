---
title: 教學課程 1：預測信用風險
titleSuffix: ML Studio (classic) - Azure
description: 一個詳盡的教學課程，說明如何在 Azure Machine Learning (傳統) 中建立適用於信用風險評估的預測性分析解決方案。 本教學課程是三部分教學課程系列的第一部分。  其中說明如何建立工作區、上傳資料及建立實驗。
keywords: 信用風險, 預測性分析解決方案, 風險評估
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 2e2edd7930ba4555748791210ad303c54f93c347
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086104"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>教學課程 1：預測信用風險 - Azure Machine Learning Studio (傳統)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

在本教學課程中，您將進一步了解開發預測性分析解決方案的程序。 您將在 Machine Learning Studio (傳統) 中建立簡單的模型。  接著，您會將該模型部署為 Azure Machine Learning Web 服務。  這個已部署的模型可使用新資料進行預測。 本教學課程是**三部分教學課程系列的第一部分**。

假設您必須根據某個人在信用申請書上提供的資訊預測其信用風險。  

信用風險評估是一個複雜的問題，但本教學課程將稍微加以簡化。 您將使用它作為範例，以說明如何使用 Microsoft Azure Machine Learning Studio (傳統) 建立預測性分析解決方案。 針對此解決方案，您將使用 Azure Machine Learning Studio (傳統) 和 Machine Learning Web 服務。  

在此三部分的教學課程中，您將從可公開取得的信用風險資料開始著手。  接著，您將開發並訓練預測模型。  最後，您會將模型部署為 Web 服務。

在教學課程的這個部分中，您將： 
 
> [!div class="checklist"]
> * 建立 Machine Learning Studio (傳統) 工作區
> * 上傳現有資料
> * 建立實驗

您隨後可使用此實驗[在第 2 部分中訓練模型](tutorial-part2-credit-risk-train.md)，然後[在第 3 部分中加以部署](tutorial-part3-credit-risk-deploy.md)。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您之前已至少使用過一次 Machine Learning Studio (傳統)，且對機器學習概念有一些了解。 但不會假設您對上述任一方面有所專精。

如果您未曾使用過 **Azure Machine Learning Studio (傳統)** ，您可以從[在 Azure Machine Learning Studio (傳統) 中建立您的第一個資料科學實驗](create-experiment.md)快速入門著手。 該快速入門會引導您完成第一次使用 Machine Learning Studio (傳統) 的程序。 它會說明基本概念，讓您了解如何將模組拖放到您的實驗、將它們連接在一起、執行實驗及檢視結果。


> [!TIP] 
> 對於您在本教學課程中開發的實驗，您可以在 [Azure AI 資源庫](https://gallery.azure.ai)中找到其工作複本。 請移至 **[教學課程 - 預測信用風險](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** ，然後按一下 [在 Studio 中開啟]  ，將實驗的複本下載到您的 Machine Learning Studio (傳統) 工作區。
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>建立 Machine Learning Studio (傳統) 工作區

若要使用 Machine Learning Studio (傳統)，您必須要有 Microsoft Azure Machine Learning Studio (傳統) 工作區。 此工作區包含您建立、管理及發行實驗所需的工具。  

若要建立工作區，請參閱[建立和共用 Azure Machine Learning Studio (傳統) 工作區](create-workspace.md)。

建立工作區後，開啟 Machine Learning Studio (傳統) ([https://studio.azureml.net/Home](https://studio.azureml.net/Home))。 如果您具有多個工作區，您可以在視窗右上角的工具列中選取工作區。

![在 Studio (傳統) 中選取工作區](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> 如果您是工作區的擁有者，您可以邀請他人到您的工作區，共用您正在執行的實驗。 您可以在 Machine Learning Studio (傳統) 中的 [設定]  頁面上執行此動作。 您只需要每個使用者的 Microsoft 帳戶或組織帳戶。
> 
> 在 [設定]  頁面上，按一下 [使用者]  ，然後按一下視窗底部的 [邀請使用者]  。
> 

## <a name="upload-existing-data"></a><a name="upload"></a>上傳現有資料

為了開發信用風險的預測模型，您需要可用來訓練和測試模型的資料。 在本教學課程中，您將使用 UCI Irvine Machine Learning Repository 中的「UCI Statlog (德國信用資料) 資料集」。 您可以在下列位置找到此儲存機制：  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

您將使用名為 **german.data** 的檔案。 將此檔案下載至您的本機硬碟。  

**german.data** 資料集包含過去 1000 名信用額度申請者的 20 個變數資料列。 這 20 個變數代表資料集的特徵集 (「特徵向量」  )，可分別提供每個信用額度申請者的識別特性。 每個資料列另外會有一個資料行代表申請者計算後的信用風險，其中有 700 名申請者被認定為低信用風險，300 名為高風險。

UCI 網站提供了這項資料的特徵向量的屬性描述。 這項資料包括財務資訊、信用歷史記錄、工作狀態、個人資訊。 每個申請者都會有一個二進位評等，指出他們屬於低信用風險還是高風險。 

您將使用這項資料來訓練預測性分析模型。 完成之後，您的模型應能夠接受新申請者的特徵向量，並預測他們屬於低信用風險還是高風險。  

以下提供一個有趣的論點。

UCI 網站上的資料集描述提及，個人信用風險若分類錯誤，將需付出多少成本。
如果模型將某個實際為低信用風險的人預測為高信用風險，則此模型做了錯誤分類。

但反向的錯誤分類對金融機構而言需要付出五倍的代價：如果模型將某個實際為高信用風險的人預測為低信用風險。

因此，您想要訓練模型，讓後一種錯誤分類的成本比另一種錯誤分類方式高出五倍。

在您的實驗中，在訓練模型時執行此動作的一個簡單方式是，複製 (五次) 代表高信用風險者的項目。 

然後，如果模型將某人錯誤分類為低信用風險，但他們實際為高風險時，模型即會會進行該相同錯誤分類五次，針對每個重複項目進行一次。 這會在訓練結果中增加此誤差的成本。


### <a name="convert-the-dataset-format"></a>轉換資料集格式

原始資料集使用以空格分隔的格式。 Machine Learning Studio (傳統) 在使用逗號分隔值 (CSV) 檔案時更能適當運作，因此您將以逗號取代空格，進行資料集轉換。  

有許多方法可以轉換此資料。 其中一種是使用下列的 Windows PowerShell 命令：   

```powershell
cat german.data | %{$_ -replace " ",","} | sc german.csv  
```

另一種方法是使用 Unix Sed 命令：  

```console
sed 's/ /,/g' german.data > german.csv
```

無論採用何種方法，您都已在名為 **german.csv** 的檔案中建立以逗號分隔的資料，供您在實驗中使用。

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>將資料集上傳至 Machine Learning Studio (傳統)

在資料轉換為 CSV 格式後，您必須將其上傳至 Machine Learning Studio (傳統) 中。 

1. 開啟 Machine Learning Studio (傳統) 首頁 ([https://studio.azureml.net](https://studio.azureml.net))。 

2. 按一下視窗左上角的功能表![功能表](./media/tutorial-part1-credit-risk/menu.png)，按一下 [Azure Machine Learning]  ，選取 [Studio]  然後登入。

3. 按一下視窗底部的 [ **+新增** ]。

4. 選取 [ **資料集**]。

5. 選取 [ **從本機檔案**]。

    ![從本機檔案新增資料集](./media/tutorial-part1-credit-risk/add-dataset.png)

6. 在 [上傳新的資料集]  對話方塊中按一下 [瀏覽]，然後尋找您已建立的 **german.csv** 檔案。

7. 輸入資料集的名稱。 在本教學課程中，我們稱之為「UCI 德國信用卡資料」。

8. 針對資料類型，請選取 **不具標頭的一般 CSV 檔案 (.nh.csv)** 。

9. 視需要新增說明。

10. 按一下 **[確定]** \(打勾記號)。  

    ![上傳資料集](./media/tutorial-part1-credit-risk/upload-dataset.png)

這會將資料上傳至您可在實驗中使用的資料集模組。

您可以管理您已上傳至 Studio (傳統) 的資料集，方法是按一下 Studio (傳統) 視窗左側的 [資料集]  索引標籤。

![管理資料集](./media/tutorial-part1-credit-risk/dataset-list.png)

如需將其他種資料類型匯入實驗的詳細資訊，請參閱[將訓練資料匯入 Azure Machine Learning Studio (傳統)](import-data.md)。

## <a name="create-an-experiment"></a>建立實驗

本教學課程的下一個步驟，是在 Machine Learning Studio (傳統) 中建立實驗，並在實驗中使用您上傳的資料集。  

1. 在 Studio (傳統) 中，按一下視窗底部的 [+新增]  。
1. 選取 [實驗]  ，然後選取 [空白實驗]。 

    ![建立新實驗](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. 選取畫布頂端的預設實驗名稱，然後將它重新命名為有意義的名稱。

    ![將實驗重新命名](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > 在 [屬性]  窗格中為實驗填入 [摘要]  和 [描述]  是不錯的作法。 這些屬性會提供記錄實驗的機會，以便稍後查看它的人員能了解您的目標和方法。
   > 
   > ![實驗屬性](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. 在實驗畫布左側的模組調色盤中，展開 [Saved Datasets]  。
1. 尋找您在 **我的資料集** 下建立的資料集並將它拖曳到畫布上。 您也可以在調色盤上方的 [搜尋]  方塊中輸入名稱，以尋找資料集。  

    ![將資料集新增至實驗](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>準備資料

您可以檢視前 100 列資料和整個資料集的部分統計資訊：按一下資料集的輸出連接埠 (底部的小圓圈)，然後選取 [視覺化]  。  

因為資料檔案並未隨附資料行標題，所以 Studio (傳統) 已提供一般標題 (Col1、Col2 等等  )。 建立模型並不一定要有良好的標題，但良好的標題可讓您更容易使用實驗中的資料。 此外，當您最終在 Web 服務中發佈此模型時，標題有助於服務的使用者識別資料行。  

您可以使用[編輯中繼資料][edit-metadata]模組來新增資料行標題。

您需使用[編輯中繼資料][edit-metadata]模組來變更與資料集關聯的中繼資料。 在此案例中，您利用它來為資料行標題提供更易記的名稱。 

若要使用[編輯中繼資料][edit-metadata]，您要先指定要修改哪些資料行 (在此案例中是全部)。接著，指定要對這些資料行執行的動作 (在此案例中是變更資料行標題)。

1. 在模組調色盤的 [搜尋]  方塊中，輸入 "metadata"。 [編輯中繼資料][edit-metadata]出現在模組清單中。

1. 按住[編輯中繼資料][edit-metadata]模組並將其拖曳到畫布上，放在您先前新增的資料集下。

1. 將資料集連線到[編輯中繼資料][edit-metadata]：按一下資料集的輸出連接埠 (資料集底部的小圓圈)、拖曳到[編輯中繼資料][edit-metadata]的輸入連接埠 (模組頂端的小圓圈)，然後放開滑鼠按鍵。 即使您在畫布上移動資料集或模組，這兩者仍會保持連線。
 
    實驗目前看起來如下：  

    ![新增編輯中繼資料](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    紅色驚嘆號標示表示您尚未設定此模組的屬性。 您接下來將執行該作業。

    > [!TIP]
    > 您可以按兩下模組並輸入文字，為模組新增註解。 這有助於您快速檢視模組在您實驗中的執行情況。 在此案例中，請按兩下[編輯中繼資料][edit-metadata]模組，然後輸入註解「新增資料行標題」。 按一下畫布上其他的任何地方來關閉文字方塊。 若要顯示註解，請按一下模組的向下箭號。
    > 
    > ![編輯中繼資料模組並新增註解](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. 選取[編輯中繼資料][edit-metadata]，然後在畫布右邊的 [屬性]  窗格中，按一下 [啟動資料行選取器]  。

1. 在 [選取資料行]  對話方塊中，選取 [可用的資料行]  中的所有資料列，然後按一下 > 將它們移到 [選取的資料行]  。
   對話方塊應該會看起來如下：

   ![已選取所有資料行的資料行選取器](./media/tutorial-part1-credit-risk/select-columns.png)


1. 按一下 **[確定]** \(打勾記號)。

1. 回到 [屬性]  窗格，找到 [新的資料行名稱]  參數。 在這個欄位中，輸入資料集中 21 個資料行的名稱清單，並以逗號加以分隔，且依資料行順序排序。 您可以從 UCI 網站上的資料集文件中取得資料行名稱，或為求簡便，您可以複製並貼上下列清單：  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   [屬性] 窗格看起來像這樣︰

   ![編輯中繼資料的屬性](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > 如果您想要確認資料行標題，請執行實驗 (按一下實驗畫布下方的 [執行]  )。 當執行結束時 ([編輯中繼資料][edit-metadata]上會出現綠色的打勾記號)，按一下[編輯中繼資料][edit-metadata]模組的輸出連接埠，然後選取 [視覺化]  。 您可以用相同方式檢視任何模組的輸出，以檢視資料在實驗中的執行進度。
   > 
   > 

### <a name="create-training-and-test-datasets"></a>建立訓練和測試資料集

您需要一些用來訓練模型的資料，和一些測試用的資料。
因此在實驗的下一個步驟中，您會將資料集分成兩個不同的資料集：一個用來訓練模型，一個用來測試模型。

為此，您將使用[分割資料][split]模組。  

1. 找到[資料分割][split]模組，將它拖曳到畫布上，然後連線到[編輯中繼資料][edit-metadata]模組。

1. 根據預設，分割率會是 0.5，並且會設定 [Randomized split]  參數。 這表示有一半資料會隨機透過[分割資料][split]模組的一個連接埠輸出，一半透過另一個連接埠輸出。 您可以調整這些參數和 [隨機種子]  參數，以變更訓練與測試資料之間的分割。 在此範例中，您將其保持原狀。
   
   > [!TIP]
   > [第一個輸出資料集內的資料列比例]  屬性會決定要透過「左側」  輸出連接埠輸出多少資料。 例如，如果您將分割率設為 0.7，則會有 70% 的資料透過左側連接埠輸出，30% 透過右側連接埠輸出。  
   > 
   > 

1. 按兩下[資料分割][split]模組，並輸入註解「訓練/測試資料分割 50%」。 

您可以依自身需求使用[分割資料][split]模組的輸出，但在範例中，我們要選擇以左側輸出作為訓練資料，右側輸出作為測試資料。  

如[上一個步驟](tutorial-part1-credit-risk.md#upload)所述，將高信用風險誤判為低風險的成本，會比將低信用風險誤判為高風險的成本多出五倍。 為了說明這一點，您將產生新資料集以反映此成本函式。 在此新資料集中，每個高風險範例都會複寫五次，而每個低風險範例則不複寫。   

您可以使用 R 程式碼來執行此複寫：  

1. 找到[執行 R 程式碼][execute-r-script]模組，並將它拖曳到實驗畫布。 

1. 將[資料分割][split]模組的左側輸出連接埠連線到[執行 R 指令碼][execute-r-script]模組的第一個輸入連接埠 ("Dataset1")。

1. 按兩下[執行 R 指令碼][execute-r-script]模組並輸入註解「設定成本調整」。

1. 在 [屬性]  窗格中，刪除 [R 指令碼]  參數中的預設文字，然後輸入此指令碼：
   
    ```r
    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")
    ```

    ![執行 R 指令碼模組中的 R 指令碼](./media/tutorial-part1-credit-risk/execute-r-script.png)

您必須為[資料分割][split]模組的每個輸出執行此一相同複寫作業，使訓練和測試資料具有相同的成本調整。 要執行此動作，最簡單的方式是複製剛製作的[執行 R 指令碼][execute-r-script]模組，並將其連線到[分割資料][split]模組的其他輸出連接埠。

1. 以滑鼠右鍵按一下[執行 R 指令碼][execute-r-script]模組，然後選取 [複製]  。

1. 以滑鼠右鍵按一下實驗畫布，然後選取 [貼上]  。

1. 將新模組拖曳至畫布中，然後將[資料分割][split]模組的右側輸出連接埠連線到這個新的[執行 R 指令碼][execute-r-script]模組的第一個輸入連接埠。 

1. 在實驗畫布底端，按一下 [執行]  。 

> [!TIP]
> 「執行 R 指令碼」模組的複本會包含與原始模組相同的指令碼。 當您在畫布上複製並貼上模組時，複本將會保留原本的所有屬性。  
> 
>

實驗此時看起來會如下所示：

![Adding Split module and R scripts](./media/tutorial-part1-credit-risk/experiment.png)

如需如何在您的實驗中使用 R 指令碼的詳細資訊，請參閱 [透過 R 擴充您的實驗](extend-your-experiment-with-r.md)。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列步驟： 
 
> [!div class="checklist"]
> * 建立 Machine Learning Studio (傳統) 工作區
> * 將現有資料上傳至工作區中
> * 建立實驗

您現在已準備就緒，可訓練和評估此資料的模型。

> [!div class="nextstepaction"]
> [教學課程 2 - 訓練和評估模型](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
