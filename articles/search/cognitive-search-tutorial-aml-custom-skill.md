---
title: 範例：建立及部署具有 Azure Machine Learning 的自訂技能
titleSuffix: Azure Cognitive Search
description: 此範例示範如何使用 Azure Machine Learning 來建立和部署 Azure 認知搜尋的 AI 擴充管線的自訂技能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745629"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>範例：使用 Azure Machine Learning 建立和部署自訂技能 

在此範例中，您將使用 Commons 授權[CC BY NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) 的「[飯店評論」資料集](https://www.kaggle.com/datafiniti/hotel-reviews) (發佈，以使用 Azure Machine Learning 從評論中將外觀型情感解壓縮以建立[自訂技能](./cognitive-search-aml-skill.md)。 這可讓您在相同評論內指派正面和負面情感，以正確歸類至已識別的實體，例如員工、會議室、大廳或集區。

若要對 Azure Machine Learning 中的層面式情感模型定型，您將使用 [nlp 食譜存放庫](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)。 然後，此模型會部署為 Azure Kubernetes 叢集上的端點。 部署之後，端點就會新增至擴充管線，做為認知搜尋服務所使用的 AML 技能。

提供了兩個資料集。 如果您想要自行對模型定型，則需要 hotel_reviews_1000.csv 檔案。 偏好略過定型步驟嗎？ 下載 hotel_reviews_100.csv。

> [!div class="checklist"]
> * 建立 Azure 認知搜尋執行個體
> * 建立 Azure Machine Learning 工作區 (搜尋服務和工作區應位於相同的訂用帳戶中)
> * 對模型定型並將其部署至 Azure Kubernetes 叢集
> * 將 AI 擴充管線連結至已部署的模型
> * 將來自已部署模型的輸出內嵌為自訂技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有 .NET SDK 支援。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - 取得[免費的訂用帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [認知搜尋服務](./search-get-started-arm.md)
* [認知服務資源](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning 工作區](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>安裝程式

* 複製或下載[範例存放庫](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)的內容。
* 如果下載是 zip 檔案，則解壓縮內容。 確定檔案可讀寫。
* 設定 Azure 帳戶和服務時，請將名稱和索引鍵複製到容易存取的文字檔案中。 名稱和索引鍵會新增至筆記本中的第一個資料格，其中會定義用來存取 Azure 服務的變數。
* 如果您不熟悉 Azure Machine Learning 及其需求，在開始使用之前，您會想要先檢閱這些文件：
 * [設定 Azure Machine Learning 的開發環境](../machine-learning/how-to-configure-environment.md)
 * [在 Azure 入口網站中建立和管理 Azure Machine Learning 工作區](../machine-learning/how-to-manage-workspace.md)
 * 設定 Azure Machine Learning 的開發環境時，請考慮使用[雲端型計算執行個體](../machine-learning/how-to-configure-environment.md#compute-instance)，以快速且輕鬆地開始使用。
* 將資料集檔案上傳至儲存體帳戶中的容器。 如果您想要在筆記本中執行定型步驟，則需要較大的檔案。 如果您偏好略過定型步驟，則建議使用較小的檔案。

## <a name="open-notebook-and-connect-to-azure-services"></a>開啟筆記本並連線到 Azure 服務

1. 將會允許存取 Azure 服務的變數所需的所有資訊，放在第一個資料格內，然後執行該儲存格。
1. 執行第二個儲存格將確認您已連線到訂用帳戶的搜尋服務。
1. 1\.1 - 1.5 小節會建立搜尋服務資料存放區、技能、索引和索引子。

此時，您可以選擇略過 Azure Machine Learning 中建立定型資料集和實驗的步驟，並直接跳到註冊 GitHub 存放庫的模型資料夾中所提供的兩個模型。 如果您略過這些步驟，請在筆記本中，跳到第 3.5 節，撰寫計分指令碼。 這麼做可節省時間；資料下載和上傳步驟最多可能需要 30 分鐘的時間才能完成。

## <a name="creating-and-training-the-models"></a>建立和對模型定型

第 2 節有六個儲存格，會從 nlp 食譜存放庫下載手套內嵌檔。 下載之後，檔案就會上傳到 Azure Machine Learning 的資料存放區。 .zip 檔案大約為 2G，執行這些工作需要一些時間。 上傳之後，接著會將定型資料解壓縮，而現在您已準備好繼續進行第 3 節。

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>對層面式情感定型，並部署您的端點

筆記本的第 3 節會對在第 2 節中建立的模型定型、註冊這些模型，並將其部署為 Azure Kubernetes 叢集中的端點。 如果您不熟悉 Azure Kubernetes，強烈建議您先檢閱下列文章，再嘗試建立推斷叢集：

* [Azure Kubernetes 服務概觀](../aks/intro-kubernetes.md)
* [Azure Kubernetes Service (AKS) 的 Kubernetes 核心概念](../aks/concepts-clusters-workloads.md)
* [Azure Kubernetes Service 中的配額、虛擬機器大小限制和區域可用性 (AKS)](../aks/quotas-skus-regions.md)

建立和部署推斷叢集最多可能需要 30 分鐘的時間。 建議您先測試 Web 服務，再繼續進行最後的步驟，並更新您的技能和執行索引子。

## <a name="update-the-skillset"></a>更新技能

筆記本中的第 4 節有四個儲存格，可更新技能和索引子。 或者，您可以使用入口網站來選取新技能，並將其套用至技能，然後執行索引子以更新搜尋服務。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

在入口網站中，移至 [技能]，然後選取 [技能定義 (JSON)] 連結。 入口網站將會顯示在筆記本的第一個資料格中建立的技能 JSON。 在顯示的右側有一個下拉式功能表，您可以在其中選取技能定義範本。 選取 Azure Machine Learning (AML) 範本。 提供 Azure ML 工作區的名稱，以及部署至推斷叢集的模型端點。 隨即會以端點的 URI 和索引鍵更新範本。

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="技能定義範本":::

從視窗複製技能範本，並將它貼上左側的技能定義中。 編輯範本以提供以下的遺漏值：

* 名稱
* 描述
* Context
* 'inputs' 名稱和來源
* 'outputs' 名稱和目標名稱

儲存技能。

儲存技能之後，移至索引子並選取 [索引子定義 (JSON)] 連結。 入口網站將會顯示在筆記本的第一個資料格中建立的索引子 JSON。 輸出欄位對應將需要以額外的欄位對應進行更新，以確保索引子可以正確地處理和傳遞它們。 儲存變更，然後選取 [執行]。 

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [檢閱自訂技能 Web API](./cognitive-search-custom-skill-web-api.md)
> [深入了解如何將自訂技能新增至擴充管線](./cognitive-search-custom-skill-interface.md)