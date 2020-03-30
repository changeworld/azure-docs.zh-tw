---
title: 將筆記本與 Azure 哨兵一起用於安全搜索
description: 本文介紹如何使用筆記本與 Azure Sentinel 搜索功能一起。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581832"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 筆記本電腦來查找安全威脅

Azure 哨兵的基礎是資料存儲;它結合了高性能查詢、動態架構和縮放到海量資料卷。 Azure 門戶和所有 Azure 哨兵工具都使用通用 API 訪問此資料存儲。 相同的 API 也可用於外部工具，如[聚居筆記本](https://jupyter.org/)和 Python。 雖然許多常見任務可以在門戶中執行，但 Jupyter 擴展了可以使用此資料執行操作的範圍。 它將完整的可程式設計性與用於機器學習、視覺化和資料分析的大量庫相結合。 這些屬性使Jupyter成為安全調查和狩獵的引人注目的工具。

![示例筆記本](./media/notebooks/sentinel-notebooks-map.png)

我們將 Jupyter 體驗集成到 Azure 門戶中，使您能夠輕鬆創建和運行筆記本來分析資料。 *Kqlmagic*庫提供膠水，允許您從 Azure Sentinel 獲取查詢並直接在筆記本中運行它們。 查詢使用[庫斯托查詢語言](https://kusto.azurewebsites.net/docs/query/index.html)。 由微軟一些安全分析師開發的幾種筆記本與 Azure Sentinel 一起打包。 其中一些筆記本是為特定方案構建的，可以作為本樣使用。 其他示例旨在說明您可以複製或調整以用於您自己的筆記本的技術和功能。 也可以從 Azure Sentinel 社區 GitHub 導入其他筆記本。

集成的 Jupyter 體驗使用[Azure 筆記本](https://notebooks.azure.com/)來存儲、共用和執行筆記本。 如果電腦上有 Python 環境和 Jupyter，或者在其他 JupterHub 環境（如 Azure 資料塊）中，也可以在本地運行這些筆記本。

筆記本有兩個元件：

- 輸入和執行查詢和代碼以及顯示執行結果的基於瀏覽器的介面。
- 負責解析和執行代碼本身的*內核*。 

預設情況下，在 Azure 筆記本中，此內核在 Azure*免費雲計算和存儲*上運行。 如果您的筆記本包含複雜的機器學習模型或視覺化效果，請考慮使用功能更強大的專用計算資源，如[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)（DSVM）。 除非您選擇共用筆記本，否則帳戶中的筆記本將保持私密。

Azure Sentinel 筆記本使用許多流行的 Python 庫，如熊貓、matplotlib、bokeh 等。 有很多其他 Python 包供您選擇，包括以下方面：

- 視覺化和圖形
- 資料處理和分析
- 統計和數值計算
- 機器學習和深度學習

我們還發佈了一些開源的Jupyter安全工具在一個名為[msticpy](https://github.com/Microsoft/msticpy/)的包。 此包用於許多包含的筆記本。 Msticpy 工具專為説明創建用於狩獵和調查的筆記本電腦而設計，我們正在積極開發新功能和改進。

初始筆記本包括：

- **引導式調查 - 進程警報**：允許您通過分析受影響主機或主機上的活動來快速分流警報。
- **引導式尋線 - Windows 主機資源管理器**：允許您流覽主機上的帳戶活動、進程執行、網路活動和其他事件。
- **引導式搜索 - Office365-探索**：在多個 Office 365 資料集中搜索可疑的 Office 365 活動。

[Azure 哨兵社區 GitHub 存儲庫](https://github.com/Azure/Azure-Sentinel)是 Microsoft 構建或由社區提供的任何未來 Azure Sentinel 筆記本的位置。

要使用筆記本，必須具有 Azure 筆記本帳戶。 有關詳細資訊，請參閱快速入門：從 Azure 筆記本文檔中[登錄並設置使用者 ID。](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) 要創建此帳戶，可以使用 Azure Sentinel - 筆記本中的命令列中的"**註冊 Azure 筆記本****"選項**：

> [!div class="mx-imgBorder"]
>![註冊 Azure 筆記本選項](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

可以從 Azure 哨兵直接運行筆記本，也可以將所有 Azure Sentinel 筆記本克隆到新的 Azure 筆記本專案。

## <a name="run-a-notebook-from-azure-sentinel"></a>從 Azure 哨兵運行筆記本
 
1. 從 Azure 門戶導航到**Azure 哨兵** > **威脅管理** > **筆記本**，您可以在其中查看 Azure Sentinel 提供的筆記本。 

2. 選擇單個筆記本來讀取其說明、所需資料類型和資料來源。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![啟動筆記本](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 選擇要使用的筆記本，然後選擇 **"啟動筆記本（預覽）"** 以克隆筆記本並將其配置為連接到 Azure Sentinel 工作區的新 Azure 筆記本專案。 該過程完成後，筆記本將在 Azure 筆記本中打開，以便您運行。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>將 Azure 哨兵筆記本克隆到新的 Azure 筆記本專案

此過程會為您創建一個 Azure 筆記本專案，其中包含 Azure Sentinel 筆記本。 然後，您可以運行筆記本，或進行更改，然後運行它們。

1. 從 Azure 門戶導航到**Azure 哨兵** > **威脅管理** > **筆記本**，然後從命令列中選擇 **"克隆筆記本**"：
  
    > [!div class="mx-imgBorder"]
    >![克隆筆記本選項](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 出現以下對話方塊時，選擇 **"導入**"以將 GitHub 存儲庫克隆到 Azure 筆記本專案中。 如果沒有現有的 Azure 筆記本帳戶，系統將提示您創建一個帳戶並登錄。

   ![導入筆記本](./media/notebooks/sentinel-notebooks-clone.png)

3. 在 **"上傳 GitHub 存儲庫"** 對話方塊中，不要**遞迴選擇"克隆"，** 因為此選項引用連結的 GitHub 存儲庫。 對於專案名稱，請使用預設名稱或鍵入新名稱。 然後按一下 **"導入**"開始克隆 GitHub 內容，這可能需要幾分鐘才能完成。

   ![導入筆記本](./media/notebooks/sentinel-create-project.png)

4. 打開剛剛創建的專案，然後打開**筆記本**資料夾以查看筆記本。 例如：

   ![導入回購](./media/notebooks/sentinel-open-notebook1.png)

然後，可以從 Azure 筆記本運行筆記本。 要從 Azure Sentinel 返回到這些筆記本，請選擇從**Azure Sentinel 中的**命令列**轉到筆記本**- 筆記本：

> [!div class="mx-imgBorder"]
>![轉到筆記本選項](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>使用筆記本進行尋線

每個筆記本都會引導您完成進行狩獵或調查的步驟。 筆記本所需的庫和其他依賴項可以從筆記本本身或通過簡單的配置過程安裝。 將筆記本專案關聯回 Azure Sentinel 訂閱的配置將在上述步驟中自動預配。

1. 如果您尚未在 Azure 筆記本中，則可以使用**Azure Sentinel - 筆記本**中的命令列中的"**轉到筆記本**"選項：
    
    > [!div class="mx-imgBorder"]
    >![轉到筆記本選項](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    在 Azure 筆記本中，選擇 **"我的專案**"，然後選擇包含 Azure Sentinel 筆記本的專案，最後選擇 **"筆記本"** 資料夾。
    
2. 在打開筆記本之前，請注意，預設情況下，選擇"自由計算"來運行筆記本：
    
   ![選擇筆記本](./media/notebooks/sentinel-open-notebook2.png)
    
    如果已配置資料科學虛擬機器 （DSVM） 以使用，請在打開第一個筆記本之前選擇 DSVM 並進行身份驗證。 

3. 選擇一個筆記本來打開它。
    
    首次打開筆記本時，系統可能會提示您選擇內核版本。 如果未提示您，則可以從**內核** >  **更改內核**中選擇內核版本，然後選擇至少 3.6 的版本。 所選內核版本顯示在筆記本視窗的右上角：
    
   ![選擇筆記本](./media/notebooks/sentinel-select-kernel.png)

4. 在對已下載的筆記本進行任何更改之前，最好先製作一份原始筆記本並處理該副本。 為此，請選擇"**檔** > **複製**"。 使用副本可讓您安全地更新到將來版本的筆記本，而無需覆蓋任何資料。
    
    現在，您可以運行或編輯所選筆記本。

建議：

- 有關在 Azure Sentinel 中查詢資料的快速介紹，請查看主**筆記本**資料夾中的[Get開始](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb)筆記本。 

- 您可以在 **"示例筆記本"** 子資料夾中找到其他示例筆記本。 這些示例筆記本已保存資料，因此更易於查看預期輸出。 我們建議在[nbviewer](https://nbviewer.jupyter.org/)中查看這些筆記本。 

- **"HowTos"** 資料夾包含描述筆記本，例如：設置預設 Python 版本、配置 DSVM、從筆記本創建 Azure Sentinel 書簽以及其他主題。

提供的筆記本既可用作有用的工具，也可用作可用於開發自己筆記本的插圖和代碼示例。

我們歡迎回饋，無論是建議、功能請求、貢獻筆記本、錯誤報表，還是對現有筆記本的改進和添加。 轉到[Azure 哨兵社區 GitHub](https://github.com/Azure/Azure-Sentinel)以創建問題或分叉並上載貢獻。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何在 Azure Sentinel 中使用 Jupyter 筆記本。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動尋找威脅](hunting.md)
- [使用書簽在狩獵時保存有趣的資訊](bookmarks.md)
