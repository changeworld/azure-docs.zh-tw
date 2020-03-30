---
title: 模型如何成為 Web 服務
titleSuffix: ML Studio (classic) - Azure
description: 概述 Azure 機器學習工作室（經典）模型從開發實驗到 Web 服務的進展機制。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217927"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>機器學習工作室（經典）模型如何從實驗發展到 Web 服務

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure 機器學習工作室（經典版）提供了一個互動式畫布，允許您開發、運行、測試和反覆運算表示預測分析模型***的實驗***。 有各種可用的模組可以︰

* 將資料輸入到實驗
* 處理資料
* 使用機器學習服務演算法來訓練模型
* 評分模型
* 評估結果
* 輸出最終值

對實驗感到滿意後，可以將其部署為經典 Azure 機器學習***Web 服務***或新的 Azure***機器學習 Web 服務***，以便使用者可以發送新資料並接收回結果。

在本文中我們將提供機制的概觀，說明 Machine Learning 模型如何從開發實驗進展為實際運作的 Web 服務。

> [!NOTE]
> 還有其他方法來開發和部署機器學習模型，但本文重點介紹了如何使用機器學習工作室（經典）。 例如，若要閱讀如何使用 R 來建立傳統預測性 Web 服務的說明，請參閱部落格文章[使用 RStudio 和 Azure Machine Learning Studio 建置和部署預測性 Web Apps](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (英文)。
>
>

雖然 Azure 機器學習工作室（經典版）旨在説明您開發和部署*預測分析模型*，但可以使用 Studio（經典）來開發不包含預測分析模型的實驗。 比方說，實驗可能只是輸入資料、操作資料，然後輸出結果。 與預測分析實驗一樣，您可以將此非預測性實驗部署為 Web 服務，但這是一個更簡單的過程，因為實驗不是訓練或評分機器學習模型。 雖然以這種方式使用 Studio（經典）並不常見，但我們將在討論中包括它，以便我們可以完整地解釋 Studio（經典）的工作原理。

## <a name="developing-and-deploying-a-predictive-web-service"></a>開發及部署預測性 Web 服務
以下是使用機器學習工作室（經典）開發和部署典型解決方案時遵循的各個階段：

![部署流程](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*圖 1 - 一般預測性分析模型的階段*

### <a name="the-training-experiment"></a>訓練實驗
***培訓實驗***是在機器學習工作室（經典）中開發 Web 服務的初始階段。 訓練實驗的目的是要提供您開發、測試、逐一查看和最終定型機器學習模型的位置。 您甚至可以在查找最佳解決方案時同時訓練多個模型，但一旦完成實驗，您將選擇單個經過訓練的模型，並從實驗中消除其餘模型。 有關開發預測分析實驗的示例，請參閱[在 Azure 機器學習工作室（經典）中為信用風險評估開發預測分析解決方案](tutorial-part1-credit-risk.md)。

### <a name="the-predictive-experiment"></a>預測性實驗
在訓練實驗中獲得經過訓練的模型後，按一下"**設置 Web 服務"** 並選擇機器學習工作室中的**預測 Web 服務**（經典），以啟動將訓練實驗轉換為***預測實驗***的過程。 預測實驗的目的是要使用您的定型模型對新資料進行計分，以最終目標成為實際運作的 Azure Web 服務。

會透過下列步驟為您完成這項轉換：

* 轉換用於定型成單一模組的模組集，並將它儲存為定型的模型
* 排除與計分無關的任何多餘模組
* 新增最終的 Web 服務將使用的輸入和輸出連接埠

可能有您想要進行的變更，以讓預測實驗備妥供部署為 Web 服務。 例如，如果想要 Web 服務輸出結果的子集，您可以在輸出連接埠之前新增篩選模組。

在此轉換程序中不會捨棄訓練實驗。 該過程完成後，在 Studio 中有兩個選項卡（經典）：一個用於訓練實驗，另一個用於預測實驗。 以此方式，在部署您的 Web 服務之前，您可以對訓練實驗進行變更，並重建預測實驗。 或者您可以儲存一份訓練實驗，以開始另一行的實驗。

> [!NOTE]
> 按一下 [預測性 Web 服務]**** 時，就會開始自動化的程序，將訓練實驗轉換為預測實驗，而且這在大部分的情況下可正常運作。 如果訓練實驗很複雜 (例如，聯結在一起的訓練有多個路徑)，您可能會想要手動進行這項轉換。 有關詳細資訊，請參閱[如何準備模型以在 Azure 機器學習工作室（經典）中部署](convert-training-experiment-to-scoring-experiment.md)。
>
>

### <a name="the-web-service"></a>Web 服務
一旦您確信預測實驗準備就緒，就可以將服務部署為經典 Web 服務或基於 Azure 資源管理器的新 Web 服務。 若要將您的模型部署為*傳統 Machine Learning Web 服務*來運作，請按一下 [部署 Web 服務]****，然後選取 [部署 Web 服務 [傳統]]****。 若要部署為*新式 Machine Learning Web 服務*，請按一下 [部署 Web 服務]****，然後選取 [部署 Web 服務 [新式]]****。 使用者現在可以使用 Web 服務 REST API 將資料傳送至您的模型中並收回結果。 如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>非一般的情況：建立非預測性 Web 服務
如果實驗未訓練預測分析模型，則無需同時創建訓練實驗和評分實驗 - 只有一個實驗，您可以將其部署為 Web 服務。 機器學習工作室（經典版）通過分析已使用的模組來檢測您的實驗是否包含預測模型。

反覆運算實驗並對此感到滿意後：

1. 按一下 [設定 Web 服務]****，並選取 [重新訓練 Web]**** - 自動會新增輸入和輸出節點
2. 按一下 [執行] ****
3. 按一下 [部署 Web 服務]****，然後根據您要部署的環境而定，選取 [部署 Web 服務 [傳統]]**** 或 [部署 Web 服務 [新式]]****。

現在已部署您的 Web 服務，而且您可以如同預測性 Web 服務一般存取及管理它。

## <a name="updating-your-web-service"></a>使用您的 Web 服務
現在，您已將實驗部署為 Web 服務，如果需要更新它，該怎麼辦？

視您需要更新的項目而定：

**您想要變更輸入或輸出，或您想要修改 Web 服務操縱資料的方式**

如果不更改模型，但只是更改 Web 服務處理資料的方式，則可以編輯預測實驗，然後按一下 **"部署 Web 服務**"，然後再次選擇 **"部署 Web 服務 [經典]** 或**部署 Web 服務 [New]"。** 將會停止 Web 服務、部署已更新的預測實驗，然後重新啟動 Web 服務。

下面是一個示例：假設您的預測實驗返回具有預測結果的整行輸入資料。 您可能決定您想要 Web 服務只傳回結果。 因此，您可以在預測性實驗中加入 **專案資料行** 模組，緊接在輸出連接埠之前，以便排除資料行而非結果。 當您按一下 [部署 Web 服務]****，然後再次選取 [部署 Web 服務 [傳統]]**** 或 [部署 Web 服務 [新式]]**** 時，Web 服務就會更新。

**您想要使用新資料重新定型模型**

如果您想要保留您的機器學習模型，但您想要以新的資料重新定型，您有兩個選擇：

1. **Web 服務執行時重新訓練模型** - 如果您想要在預測性 Web 服務執行時重新訓練模型，您可以藉由對訓練實驗進行幾個修改，使它成為***重新訓練實驗***，然後可以將它部署為***重新訓練 Web* 服務**。 如需如何執行這項操作的指示，請參閱 [以程式設計方式重新定型機器學習服務模型](/azure/machine-learning/studio/retrain-machine-learning-model)。
2. **返回原始訓練實驗並使用不同的訓練資料來開發您的模型** - 您的預測實驗連結至 Web 服務，但訓練實驗未以這種方式直接連結。 如果您修改原始的訓練實驗，並按一下 [設定 Web 服務]****，它會建立「新的」** 預測實驗，部署時將會建立「新的」** Web 服務。 它不只是更新原始 Web 服務。

   如果您需要修改訓練實驗，請開啟它並按一下 [另存新檔]**** 以製作複本。 這將會原始的訓練實驗、預測實驗和 Web 服務維持不變。 您現在可以利用您的變更建立新的 Web 服務。 部署新 Web 服務後，可以決定是否停止以前的 Web 服務，還是將其與新 Web 服務一起運行。

**您想要定型不同的模型**

如果您想要對原始預測性實驗進行變更，例如選取不同的機器學習演算法、嘗試不同的定型方法等，則您必須遵循上述用於重新定型模型的第二個程序：開啟訓練實驗、按一下 [另存新檔] **** 來製作副本，然後開始開發模型的新路徑、建立預測性實驗和部署 Web 服務。 這會建立與原始無關的新 Web 服務 - 您可以決定要繼續執行哪一個或兩者。

## <a name="next-steps"></a>後續步驟
如需開發和實驗程序的詳細資訊，請參閱下列文章︰

* 轉換實驗 -[如何準備模型以部署在 Azure 機器學習工作室（經典）](convert-training-experiment-to-scoring-experiment.md)
* 部署 Web 服務 - [部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)
* 重新定型模型 - [以程式設計方式重新定型機器學習服務模型](/azure/machine-learning/studio/retrain-machine-learning-model)

如需整個程序的範例，請參閱：

* [機器學習教程：在 Azure 機器學習工作室創建第一個實驗（經典）](create-experiment.md)
* [演練：為 Azure 機器學習中的信用風險評估開發預測分析解決方案](tutorial-part1-credit-risk.md)

