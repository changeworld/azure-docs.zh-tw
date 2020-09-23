---
title: 使用 Notebook 搭配 Azure Sentinel 進行安全性搜捕
description: 本文說明如何使用 Notebook 搭配 Azure Sentinel 搜捕功能。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: ded332813a840892f640aa6f6e48debbfe381b4b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889278"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter Notebook 尋找安全性威脅

Azure Sentinel 的基礎是資料存放區;它結合高效能查詢、動態架構，以及調整為大量資料磁片區。 Azure 入口網站和所有 Azure Sentinel 工具都會使用通用 API 來存取此資料存放區。 相同的 API 也適用於外部工具，例如 [Jupyter](https://jupyter.org/) Notebook 和 Python。 雖然在入口網站中可以執行許多常見工作，但 Jupyter 會擴充您可使用此資料來執行的作業範圍。 其結合了完整的可程式性與大量程式庫集合，以便進行機器學習、視覺化和資料分析。 這些屬性讓 Jupyter 成為安全性調查和搜捕的強大工具。

![範例 Notebook](./media/notebooks/sentinel-notebooks-map.png)

我們已將 Jupyter 體驗整合到 Azure 入口網站中，讓您得以輕鬆地建立及執行 Notebook 來分析您的資料。 *Kqlmagic* 程式庫提供的黏附功能，可讓您從 Azure Sentinel 進行查詢，並直接在 Notebook 內執行查詢。 查詢會使用 [Kusto 查詢語言](https://kusto.azurewebsites.net/docs/query/index.html)。 某些由 Microsoft 的安全性分析師開發的多個 Notebook 會與 Azure Sentinel 封裝在一起。 其中有些 Notebook 是針對特定案例而建立，可按現況使用。 其他則是作為範例，解釋您可複製或改寫以在自己的 Notebook 中使用的技術和功能。 其他筆記本也可以從 Azure Sentinel 的社區 GitHub 匯入。

整合式 Jupyter 體驗會使用 [Azure Notebooks](https://notebooks.azure.com/) 來儲存、共用和執行 Notebook。 如果您在電腦上或在其他 JupterHub 環境 (例如 Azure Databricks) 中有 Python 環境和 Jupyter，也可以在本機執行這些 Notebook。

Notebook 有兩個元件：

- 瀏覽器型介面，您可在其中輸入並執行查詢和程式碼，以及顯示執行的結果。
- 負責剖析和執行程式碼本身的「核心程序」。

Azure Sentinel 筆記本的核心會在 Azure 虛擬機器上執行)  (VM。 如果您的筆記本包含複雜的機器學習模型，則有數種授權選項可利用功能更強大的虛擬機器。

Azure Sentinel Notebook 會使用許多熱門的 Python 程式庫，例如 pandas、matplotlib、bokeh 和其他程式庫。 有很多其他 Python 套件可供您選擇，涵蓋的範圍如下：

- 視覺效果和圖形
- 資料處理和分析
- 統計資料和數值計算
- 機器學習和深度學習

我們也在名為 [msticpy](https://github.com/Microsoft/msticpy/) 的套件中，發行了一些開放原始碼的 Jupyter 安全性工具。 此套件使用於許多內含的 Notebook。 Msticpy 工具的具體設計訴求是要協助建立用於搜捕和調查的 Notebook，而且我們正積極開發新功能和改進功能。

[Azure Sentinel 社群 GitHub 存放庫](https://github.com/Azure/Azure-Sentinel)是 Microsoft 所建置或該社群所提供的任何未來 Azure Sentinel Notebook 位置。

若要使用筆記本，您必須先建立 Azure Machine Learning (ML) 工作區。

## <a name="create-an-azure-ml-workspace"></a>建立 Azure ML 工作區

1. 在 Azure 入口網站中，流覽至**Azure Sentinel**  >  **威脅管理**  >  **筆記本**，然後選取 [**啟動筆記本**]。

    > [!div class="mx-imgBorder"]
    > ![啟動筆記本以啟動 azure ml 工作區](./media/notebooks/sentinel-notebooks-launch.png)

1. 在 [ **AzureML] 工作區**底下，選取 [ **建立新**的]。

    > [!div class="mx-imgBorder"]
    > ![建立工作區](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. 在 [ **Machine Learning** ] 頁面上提供下列資訊，然後選取 [ **審核 + 建立**]。

    |欄位|描述|
    |--|--|
    |訂用帳戶|選取您要使用的 Azure 訂用帳戶。|
    |資源群組|在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **AzureMLRG**。|
    |工作區名稱|輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **testworkspace1**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。|
    |區域|選取最接近您的使用者與資料資源的位置，以建立工作區。|
    |工作區版本|選取 [ **基本** ] 作為此範例中的工作區類型。 工作區類型 (基本 & Enterprise) 決定您將擁有存取權和定價的功能。|

    > [!div class="mx-imgBorder"]
    > ![提供工作區詳細資料](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. 請檢查資訊、確認資訊正確無誤，然後選取 [ **建立** ] 以開始部署您的工作區。

    > [!div class="mx-imgBorder"]
    > ![查看工作區詳細資料](./media/notebooks/sentinel-notebooks-azureml-review.png)

    在雲端中建立工作區可能需要幾分鐘的時間，在這段期間，[ **總覽** ] 頁面會顯示目前的部署狀態。

    > [!div class="mx-imgBorder"]
    > ![工作區部署](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

部署完成後，您就可以在新的 Azure ML 工作區中啟動筆記本。

> [!div class="mx-imgBorder"]
> ![工作區部署成功](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>使用您的 Azure ML 工作區啟動筆記本

1. 在 Azure 入口網站中，瀏覽至 [Azure Sentinel] > [威脅管理] > [Notebook]，您可以在其中看到 Azure Sentinel 提供的 Notebook。

    > [!TIP]
    > 選取 [ **輔助線] & 意見** 反應，開啟具有筆記本其他說明和指引的窗格。
    > ![查看筆記本指南](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. 選取個別的筆記本以查看其描述、必要的資料類型和資料來源。

    > [!div class="mx-imgBorder"]
    > ![查看筆記本詳細資料](./media/notebooks/sentinel-azure-notebooks-view.png)

1. 選取您要使用的筆記本，然後選取 [ **啟動筆記本** ]，將筆記本複製並設定為連接到您 Azure Sentinel 工作區的新 Azure Notebooks 專案。 當程序完成時，Notebook 會在 Azure Notebooks 內開啟以供您執行。

    > [!div class="mx-imgBorder"]
    > ![選取筆記本](./media/notebooks/sentinel-azure-notebooks-select.png)

1. 在 [AzureML] 工作區底下，選取您的 Azure ML 工作區，然後選取 [ **啟動**]。

    > [!div class="mx-imgBorder"]
    > ![啟動 Notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. 選取計算實例。 如果您沒有計算實例，請執行下列動作：
    1. 選取加號 (+) ，以啟動 [ **新增計算實例** ] wizard。

        > [!div class="mx-imgBorder"]
        > ![啟動計算實例 wizard](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. 在 [ **新增計算實例** ] 頁面上，提供必要的資訊，然後選取 [ **建立**]。

        > [!div class="mx-imgBorder"]
        > ![建立計算實例](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. 建立筆記本伺服器之後，請在每個儲存格中選取執行圖示，以在筆記本中執行程式碼。

    > [!div class="mx-imgBorder"]
    > ![執行筆記本](./media/notebooks/sentinel-azure-notebooks-run.png)

建議：

- 如需在 Azure Sentinel 中查詢資料的快速簡介，請參閱 [使用 AZURE ML 筆記本的消費者入門，並 Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) 指南。

- 您可以在 [**範例筆記本**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) GitHub 子資料夾中找到其他範例筆記本。 這些範例 Notebook 已與資料一起儲存，讓您更輕鬆地查看預期的輸出。 我們建議您在 [nbviewer](https://nbviewer.jupyter.org/) 中檢視這些 Notebook。

- [**做法**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos)GitHub 子資料夾包含說明的筆記本，例如：設定您的預設 Python 版本、設定 DSVM、從筆記本建立 Azure Sentinel 書簽，以及其他主題。

提供的 Notebook 主要作為有用的工具，以及您可在開發自己的 Notebook 時使用的圖例和程式碼範例。

我們歡迎您提供意見反應、建議、功能要求、貢獻的 Notebook、錯誤 (bug) 報告，或現有 Notebook 的改進和新增項目。 請移至 [Azure Sentinel 社群 GitHub](https://github.com/Azure/Azure-Sentinel) 以建立問題或分支並上傳貢獻項目。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何開始在 Azure Sentinel 中使用 Jupyter Notebook。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動搜捕威脅](hunting.md)
- [搜捕時使用書籤來儲存感興趣的資訊](bookmarks.md)
