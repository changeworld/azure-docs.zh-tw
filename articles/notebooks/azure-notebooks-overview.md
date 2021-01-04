---
title: Azure Notebooks 預覽概觀
description: 使用不需要安裝或設定的免費 Azure Notebooks 預覽服務，在雲端中執行 Jupyter Notebook。
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: 7e622bdef785085384395bb0c8ac3efba2b2053a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400248"
---
# <a name="overview-of-azure-notebooks-preview"></a>Azure Notebooks 預覽概觀

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks 是免費的裝載服務，無需任何安裝程序，就能用來在雲端中開發和執行 Jupyter Notebooks。 [Jupyter](https://jupyter.org/) (前稱 IPython) 是開放原始碼專案，可輕鬆將 Markdown 文字、可執行程式碼、持續性資料、圖形以及視覺效果統整至名為「筆記本」(影像由 jupyter.org 提供) 的單一且可分享的畫布上：

[![Jupyter Notebook 的範例](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

因為程式碼、圖形和說明文字的強大組合，Jupyter 成為了許多用途的熱門選擇，這些用途包括資料科學教學、資料清除和轉換、數值模擬、統計模型和開發機器學習模型。

## <a name="hassle-free-experience"></a>輕鬆無比的體驗

Azure Notebooks 可協助您快速開始使用原型化、資料科學、學術研究或學習編寫程式 Python：

- 資料科學家不必安裝即可立即存取完整的 Anaconda 環境。
- 老師可以為學生提供輕鬆無比的 Python 環境。
- 演講人可以提供線上對談或網路研討會，而不必請出席者花 45 分鐘安裝軟體。
- 開發人員或業餘人士可以使用 Notebooks 作為快速的程式碼剪貼簿。

當人們透過可由瀏覽器存取的雲端服務 (如 Azure Notebooks (預覽版)) 對筆記本共同作業時，筆記本會變得更有威力。 在雲端中，使用者既不需要在本機安裝 Jupyter，也不需要擔憂環境的維護。 雲端也可讓您輕鬆地與其他授權使用者共用筆記本 (和相關聯的資料檔案)，而不必以複雜的方式透過外部途徑 (如原始檔控制存放庫) 共用筆記本。 使用 Azure Notebooks，使用者也可以將筆記本複製 (或「克隆」) 到自己的帳戶以進行修改或測試，如果您要進行教學，這對您特別有用。

由於 Azure Notebooks 是通用的程式碼撰寫、執行和共用平台，所以您可以將它用於許多不同的案例：

- 學習新的程式設計語言 - 試試其中一個 [FrontPage 教學課程](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- 學習資料科學 - 試試 [Jake VanderPlas 所寫的書](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- 對數百名學生[講授課程](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- 在線上或會議中提供網路研討會，而不需要花時間安裝軟體 
- 讓 GitHub 使用者可以藉由[建立 GitHub 啟動徽章](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)來直接載入和執行筆記本
- 提供 [PowerPoint 之類的投影片](https://notebooks.azure.com/help/jupyter-notebooks/slides)，且這些投影片中的程式碼可供執行！

簡單地說，Azure Notebooks 可協助您更有效率地完成工作，進而實現更多目標。

> [!Note]
> 如需 Jupyter 本身的詳細資訊，請參閱 [jupyter.org](https://jupyter.org/) 和 [Jupyter 文件](https://jupyter-notebook.readthedocs.io/en/latest/)。

## <a name="pricing-and-quotas"></a>定價和配額

Azure Notebooks 是一項免費服務，但為防濫用，每個專案都限制只能使用 4 GB 的記憶體和 1 GB 的資料。 超過這些限制的合法使用者會看到 Captcha 查問，通過後才能繼續執行筆記本。

若要解除所有限制，請以使用 Azure Active Directory 的帳戶 (例如公司帳戶) 登入 Azure Notebooks。 如果該帳戶與 Azure 訂用帳戶相關聯，則您可以連線至該訂用帳戶內的任何 Azure 資料科學虛擬機器執行個體。 

Notebook 伺服器保證會存在最多 8 小時。 在大部分情況下，容器不受此限制，並且會繼續執行超過這個時間，但長時間執行的工作階段可能偶爾會關閉以確保系統穩定。

## <a name="available-kernels-and-environments"></a>可用的核心和環境

針對每個筆記本，您要選取用來執行任何程式碼單元的核心 (也就是執行階段環境)。 Azure Notebooks 支援下列核心：

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (即將淘汰)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks 也會包含基底散發套件以外的額外套件。 例如，Python 核心便包含 numpy、pandas、scikit-learn、matplotlib 和 bokeh 程式庫。

您也可以自訂專案，建立適合該專案中所有筆記本的環境。

除了基底散發套件外，Azure Notebooks 還預先安裝了許多適合資料科學家使用的額外套件。 您也可以針對每種語言使用典型程序來安裝您自己的套件。

## <a name="pre-configured-jupyter-extensions"></a>預先設定好的 Jupyter 擴充功能

Azure Notebooks 已預先設定下列 Jupyter 擴充功能：

- [RISE](https://github.com/damianavila/RISE)：Jupyter 投影片擴充功能 (也稱為 live_reveal)。
- [JupyterLab](https://github.com/jupyterlab/jupyterlab)：用來與 Jupyter Notebook 搭配運作的完整計算環境。
- [Altair](https://github.com/ellisonbg/altair)：適用於 Python 的宣告式統計視覺化程式庫。
- [BQPlot](https://github.com/bloomberg/bqplot)：適用於 Jupyter 筆記本的互動式繪圖架構。
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets)：適用於 Jupyter 筆記本的互動式 HTML 小工具。

## <a name="issues-and-getting-help"></a>問題和取得協助

由於 Azure Notebooks 服務仍為預覽版，因此和其他 Azure 服務相比，遇到暫時中斷的情形可能會更為頻繁或持續更久。 某些功能可能不完整或包含錯誤。

目前，建議您不要將 Azure Notebooks 用於商務關鍵性應用程式或機密的筆記本和資料。

若要討論您的 Azure Notebooks 相關問題，請在 [GitHub 存放庫](https://github.com/Microsoft/AzureNotebooks/issues)中提出問題。

## <a name="next-steps"></a>後續步驟  

- 快速入門：

  - [登入並設定使用者識別碼](quickstart-sign-in-azure-notebooks.md)
  - [匯出 Jupyter Notebook](quickstart-export-jupyter-notebook-project.md)
