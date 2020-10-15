---
title: ML Studio (傳統版)：從範例開始實驗 - Azure
description: 了解如何透過 Azure AI 資源庫和 Azure Machine Learning Studio (傳統版) 使用範例機器學習服務實驗來建立新的實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: d62b958189ac01f1beabdbf17ee5cc6a08481e23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362294"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>從 Azure AI 資源庫中的工作範例建立 Azure Machine Learning Studio (傳統版) 實驗

**適用於：** ![適用於。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)   ![不適用於。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)




了解如何從 [Azure AI 資源庫](https://gallery.azure.ai/)的範例實驗開始，而不是從頭建立機器學習服務實驗。 您可以使用範例來建立自己的機器學習服務解決方案。

資源庫有 Microsoft Azure Machine Learning Studio (傳統版) 小組的範例實驗，以及由 Machine Learning 社群共用的範例。 您也可以提出有關實驗的問題或張貼意見。

若要查看如何使用資源庫，請觀看[初學者的資料科學](data-science-for-beginners-the-5-questions-data-science-answers.md)系列中的 3 分鐘影片[複製其他人的工作來進行資料科學](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)。



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>在 Azure AI 資源庫中尋找要複製的實驗
若要查看有哪些可用的實驗，請移至[資源庫](https://gallery.azure.ai/)，然後按一下頁面頂端的 [實驗]。

### <a name="find-the-newest-or-most-popular-experiments"></a>尋找最新或最受歡迎的實驗
在此頁面上，您可以查看 [最近新增] 實驗，或往下捲動查看 [熱門項目] 或最新的 [熱門的 Microsoft 實驗]。

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>尋找符合特定需求的實驗
若要瀏覽所有實驗︰

1. 按一下頁面頂端的 [全部瀏覽]  。
2. 在左側 [類別] 區段的 [精簡依據] 之下，選取 [實驗] 以查看資源庫中的所有實驗。
3. 有幾種不同方式可以找到符合您需求的實驗︰
   * **選取左邊的篩選。** 例如，若要瀏覽使用 PCA 型異常偵測演算法的實驗：按一下 [類別] 之下的 [實驗]。 然後，在 [使用的演算法] 之下，按一下 [全部顯示]，並在對話方塊中選擇 [以 PCA 為基礎的異常偵測]。 您可能必須捲動才能看到它。<br></br>
     ![選取篩選條件](./media/sample-experiments/choose-an-algorithm.png)
   * **使用 [搜尋] 方塊。** 例如，若要尋找 Microsoft 所提出並使用二級支援向量機器演算法的數字辨識相關實驗，請在 [搜尋] 方塊中輸入「數字辨識」。 然後選取篩選器 [實驗]、[僅包含 Microsoft 內容] 和 [二級支援向量機器]：<br></br>
     ![使用搜尋方塊](./media/sample-experiments/search-for-experiments.png)
4. 按一下實驗，以深入了解相關資訊。
5. 若要執行和 (或) 修改實驗，請按一下實驗頁面上的 [在 Studio 中開啟]  。 <br></br>

    ![範例實驗](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>使用範例作為範本來建立新實驗
您也可以使用資源庫範例作為範本，在 Machine Learning Studio (傳統版) 中建立新實驗。

1. 用您的 Microsoft 帳戶認證登入 [Studio](https://studio.azureml.net)，然後按一下 [新增] 以建立實驗。
2. 瀏覽範例內容，然後按一下其中一個。

隨即以範例實驗作為範本，在您的 Machine Learning Studio (傳統版) 工作區中建立新的實驗。

## <a name="next-steps"></a>後續步驟
* [從各種資源匯入資料](import-data.md)
* [部署 Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)
