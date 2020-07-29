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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652023"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter Notebook 尋找安全性威脅

Azure Sentinel 的基礎是資料存放區；其結合了高效能查詢、動態結構描述，並可擴充為大量資料磁碟區。 Azure 入口網站和所有 Azure Sentinel 工具都會使用通用 API 來存取此資料存放區。 相同的 API 也適用於外部工具，例如 [Jupyter](https://jupyter.org/) Notebook 和 Python。 雖然在入口網站中可以執行許多常見工作，但 Jupyter 會擴充您可使用此資料來執行的作業範圍。 其結合了完整的可程式性與大量程式庫集合，以便進行機器學習、視覺化和資料分析。 這些屬性讓 Jupyter 成為安全性調查和搜捕的強大工具。

![範例 Notebook](./media/notebooks/sentinel-notebooks-map.png)

我們已將 Jupyter 體驗整合到 Azure 入口網站中，讓您得以輕鬆地建立及執行 Notebook 來分析您的資料。 *Kqlmagic* 程式庫提供的黏附功能，可讓您從 Azure Sentinel 進行查詢，並直接在 Notebook 內執行查詢。 查詢會使用 [Kusto 查詢語言](https://kusto.azurewebsites.net/docs/query/index.html)。 某些由 Microsoft 的安全性分析師開發的多個 Notebook 會與 Azure Sentinel 封裝在一起。 其中有些 Notebook 是針對特定案例而建立，可按現況使用。 其他則是作為範例，解釋您可複製或改寫以在自己的 Notebook 中使用的技術和功能。 您也可以從 Azure Sentinel 社群 GitHub 匯入其他 Notebook。

整合式 Jupyter 體驗會使用 [Azure Notebooks](https://notebooks.azure.com/) 來儲存、共用和執行 Notebook。 如果您在電腦上或在其他 JupterHub 環境 (例如 Azure Databricks) 中有 Python 環境和 Jupyter，也可以在本機執行這些 Notebook。

Notebook 有兩個元件：

- 瀏覽器型介面，您可在其中輸入並執行查詢和程式碼，以及顯示執行的結果。
- 負責剖析和執行程式碼本身的「核心程序」。 

在 Azure Notebooks 中，此核心程序預設會在 Azure *免費雲端計算和儲存體*上執行。 如果您的 Notebook 包含複雜的機器學習模型或視覺效果，請考慮使用功能更強大的專用計算資源，例如[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM)。 除非您選擇共用，否則您帳戶中的 Notebook 會保持私密。

Azure Sentinel Notebook 會使用許多熱門的 Python 程式庫，例如 pandas、matplotlib、bokeh 和其他程式庫。 有很多其他 Python 套件可供您選擇，涵蓋的範圍如下：

- 視覺效果和圖形
- 資料處理和分析
- 統計資料和數值計算
- 機器學習和深度學習

我們也在名為 [msticpy](https://github.com/Microsoft/msticpy/) 的套件中，發行了一些開放原始碼的 Jupyter 安全性工具。 此套件使用於許多內含的 Notebook。 Msticpy 工具的具體設計訴求是要協助建立用於搜捕和調查的 Notebook，而且我們正積極開發新功能和改進功能。

初始 Notebook 包括：

- **引導式調查 - 程序警示**：可讓您藉由分析受影響主機上的活動，快速將警示分級。
- **引導式搜捕 - Windows 主機總管**：可讓您探索帳戶活動、程序執行、網路活動和主機上的其他事件。
- **引導式搜補 - Office365 探索**：在多個 Office 365 資料集中搜捕可疑的 Office 365 活動。

[Azure Sentinel 社群 GitHub 存放庫](https://github.com/Azure/Azure-Sentinel)是 Microsoft 所建置或該社群所提供的任何未來 Azure Sentinel Notebook 位置。

若要使用 Notebook，您必須擁有 Azure Notebooks 帳戶。 如需詳細資訊，請參閱[快速入門：登入並設定使用者識別碼](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) (Azure Notebooks 文件)。 若要建立此帳戶，您可以從 [Azure Sentinel - Notebooks] 中的命令列，使用 [註冊 Azure Notebooks] 選項：

> [!div class="mx-imgBorder"]
>![註冊 Azure Notebooks 選項](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

您可以直接從 Azure Sentinel 執行 Notebook，或將所有 Azure Sentinel Notebook 複製到新的 Azure Notebooks 專案。

## <a name="run-a-notebook-from-azure-sentinel"></a>從 Azure Sentinel 執行 Notebook
 
1. 在 Azure 入口網站中，瀏覽至 [Azure Sentinel] > [威脅管理] > [Notebook]，您可以在其中看到 Azure Sentinel 提供的 Notebook。 

2. 選取個別的 Notebook 以讀取其描述、必要的資料類型和資料來源。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![啟動 Notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 選取您要使用的 Notebook，然後選取 [啟動 Notebook (預覽)]，將 Notebook 複製到新的 Azure Notebooks 專案並加以設定，以連線到您的 Azure Sentinel 工作區。 當程序完成時，Notebook 會在 Azure Notebooks 內開啟以供您執行。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>將 Azure Sentinel Notebook 複製到新的 Azure Notebooks 專案

此程序會為您建立 Azure Notebooks 專案，其中包含 Azure Sentinel Notebook。 然後您可以按現況執行 Notebook，或對其進行變更，然後加以執行。

1. 在 Azure 入口網站中，瀏覽至 [Azure Sentinel] > [威脅管理] > [Notebook]，然後從命令列選取 [複製 Notebook]：
  
    > [!div class="mx-imgBorder"]
    >![複製 Notebook 選項](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 當下列對話方塊出現時，請選取 [匯入]，將 GitHub 存放庫複製到您的 Azure Notebooks 專案。 如果您沒有現有的 Azure Notebooks 帳戶，系統會提示您建立帳戶並登入。

   ![匯入 Notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. 在 [上傳 GitHub 存放庫] 對話方塊上，不要選取 [以遞迴方式複製]，因為此選項參考連結的 GitHub 存放庫。 針對專案名稱，使用預設名稱或輸入新的名稱。 然後按一下 [匯入] 以開始複製 GitHub 內容，這可能需要幾分鐘的時間才能完成。

   ![匯入 Notebook](./media/notebooks/sentinel-create-project.png)

4. 開啟您剛建立的專案，然後開啟 **Notebooks** 資料夾來查看 Notebook。 例如：

   ![匯入存放庫](./media/notebooks/sentinel-open-notebook1.png)

然後，您可以從 Azure Notebooks 執行 Notebook。 若要從 Azure Sentinel 返回這些 Notebook，請從 [Azure Sentinel - Notebooks] 中的命令列，選取 [前往您的 Notebook]：

> [!div class="mx-imgBorder"]
>![前往您的 Notebooks 選項](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>使用 Notebook 來搜捕

每個 Notebook 都會引導您完成進行搜捕或調查的步驟。 Notebook 所需的程式庫和其他相依性可經由 Notebook 本身或透過簡單的設定程序來安裝。 將您的 Notebook 專案繫結回到您的 Azure Sentinel 訂用帳戶的設定，已在先前的步驟中自動佈建。

1. 如果您尚未在 Azure Notebooks 中，可以從 [Azure Sentinel - Notebooks] 中的命令列，使用 [前往您的 Notebooks] 選項：
    
    > [!div class="mx-imgBorder"]
    >![前往您的 Notebooks 選項](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    在 Azure Notebooks 中，選取 [我的專案]，然後選取包含 Azure Sentinel Notebook 的專案，最後選取 **Notebooks** 資料夾。
    
2. 開啟 Notebook 之前，請注意，預設會選取 [免費計算] 來執行 Notebook：
    
   ![選取 Notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    如果您已如簡介中所述設定要使用的資料科學虛擬機器 (DSVM)，請先選取 DSVM 並進行驗證，再開啟第一個 Notebook。 

3. 選取 Notebook 加以開啟。
    
    第一次開啟 Notebook 時，系統可能會提示您選取核心版本。 如果未出現提示，您可以從 [核心] >  [變更核心] 選取核心版本，然後選取至少為 3.6 的版本。 所選的核心版本會顯示在 Notebook 視窗的右上方：
    
   ![選取 Notebook](./media/notebooks/sentinel-select-kernel.png)

4. 在您對所下載的 Notebook 進行任何變更之前，最好先製作原始 Notebook 的複本並使用該複本。 若要這麼做，請選取 [檔案] > [製作複本]。 使用複本可讓您安全地更新成未來版本的 Notebook，而不會覆寫任何資料。
    
    您現在已經準備好執行或編輯所選的 Notebook。

建議：

- 如需在 Azure Sentinel 中查詢資料的快速簡介，請查看主要 **Notebooks** 資料夾中的 [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) Notebook。 

- 您可以在 **Sample-Notebooks** 子資料夾中找到其他範例 Notebook。 這些範例 Notebook 已與資料一起儲存，讓您更輕鬆地查看預期的輸出。 我們建議您在 [nbviewer](https://nbviewer.jupyter.org/) 中檢視這些 Notebook。 

- **HowTos** 資料夾包含描述下列各項的 Notebook，例如：設定預設 Python 版本、設定 DSVM、從 Notebook 建立 Azure Sentinel 書籤，以及其他主題。

提供的 Notebook 主要作為有用的工具，以及您可在開發自己的 Notebook 時使用的圖例和程式碼範例。

我們歡迎您提供意見反應、建議、功能要求、貢獻的 Notebook、錯誤 (bug) 報告，或現有 Notebook 的改進和新增項目。 請移至 [Azure Sentinel 社群 GitHub](https://github.com/Azure/Azure-Sentinel) 以建立問題或分支並上傳貢獻項目。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何開始在 Azure Sentinel 中使用 Jupyter Notebook。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動搜捕威脅](hunting.md)
- [搜捕時使用書籤來儲存感興趣的資訊](bookmarks.md)
