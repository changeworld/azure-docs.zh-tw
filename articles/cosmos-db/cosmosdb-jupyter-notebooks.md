---
title: Azure Cosmos DB 中的內建 Jupyter Notebooks 支援簡介 (預覽)
description: 了解如何在 Azure Cosmos DB 中使用內建 Jupyter Notebooks 支援，以互動方式執行查詢。
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588150"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Azure Cosmos DB 中的內建 Jupyter Notebooks 支援 (預覽)

Jupyter Notebook 是一個開放原始碼 Web 應用程式，可讓您建立及共用含有即時程式碼、方程式、視覺效果和敘事文字的文件。 

Azure Cosmos DB 內建的 Jupyter Notebooks 會直接整合到 Azure 入口網站和您的 Azure Cosmos DB 帳戶，使其方便且便於使用。 開發人員、資料科學家、工程師和分析師可使用熟悉的 Jupyter Notebooks 體驗進行資料探索、資料清理、資料轉換、數值模擬、統計建模、資料視覺效果，以及機器學習。

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Azure Cosmos DB 中的 Jupyter Notebooks 視覺效果":::

Azure Cosmos DB 支援適用於所有 API 的 C# 和 Python 筆記本，包括 Core (SQL)、Cassandra、Gremlin、Table 和適用於 MongoDB 的 API。 在筆記本中，您可以利用內建的命令和功能，輕鬆地建立 Azure Cosmos DB 資源、上傳資料，以及在 Azure Cosmos DB 中查詢並將資料視覺化。 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Azure Cosmos DB 中的 Jupyter Notebooks 支援":::

## <a name="benefits-of-jupyter-notebooks"></a>Jupyter Notebooks 的優點

Jupyter Notebooks 最初是針對以 Python 和 R 撰寫的資料科學應用程式而開發。不過，其可針對不同種類的專案以各種方式使用，包括：

**資料視覺效果：** Jupyter Notebooks 可讓您以共用筆記本的形式將資料視覺化，將資料集呈現為圖形。 您可以建立視覺效果、對共用程式碼和資料集進行互動式變更，以及共用結果。

**程式碼共用：** GitHub 之類的服務可提供一些共用程式碼的方式，但大部分不是互動式。 有了 Jupyter Notebooks，您可以檢視程式碼、加以執行，並直接在 Azure 入口網站中顯示結果。

**與程式碼的即時互動：** Jupyter Notebooks 中的程式碼是動態的；您可以加以編輯，並即時以累加方式執行更新。 您也可以內嵌使用者控制項 (例如滑杆或文字輸入欄位)，以作為程式碼、示範或概念證明 (POC) 的輸入來源。

**程式碼範例的文件和資料探索的結果：** 如果您有一段程式碼且想要逐行解說運作方式，可以將其內嵌在 Jupyter Notebook 中。 您可以同時新增互動性和文件。

**適用於 Azure Cosmos DB 的內建命令：** Azure Cosmos DB 的內建魔術命令，可讓您輕鬆地與帳戶互動。 您可以使用 %%upload 和 %%sql 之類的命令，將資料上傳至容器，並使用 [SQL API 語法](sql-query-getting-started.md)加以查詢。 您不需要撰寫額外的自訂程式碼。

**集中管理環境：** Jupyter Notebooks 將程式碼、RTF、影像、影片、動畫、數學方程式、繪圖、地圖、互動式圖表、小工具和圖形化使用者介面結合成單一文件。

## <a name="components-of-a-jupyter-notebook"></a>Jupyter Notebooks 的元件

Jupyter Notebooks 可以包含數種類型的元件，每個元件都會組織成不同的區塊或儲存格：

**文字和 HTML：** 在任何時間點，都可以將純文字或在 Markdown 語法中標註要產生 HTML 的文字插入文件中。 CSS 樣式也可以內嵌加入，或新增至用來產生筆記本的範本。

**程式碼和輸出：** Jupyter Notebooks 支援 Python 和 C# 程式碼。 已執行程式碼的結果會緊接在程式碼區塊之後，而且程式碼區塊可依您想要的順序執行很多次。

**視覺效果：** 您可以使用 Matplotlib、Plotly、Bokeh 和其他模組，從程式碼產生圖形和圖表。 這些視覺效果類似於輸出，會以內嵌方式出現在產生它們的程式碼旁邊。 這些視覺效果類似於輸出，會以內嵌方式出現在產生它們的程式碼旁邊。

**多媒體：** 由於 Jupyter Notebooks 是以 Web 技術為基礎而建置，因此可以顯示網頁中支援的各種多媒體類型。 您可以 HTML 元素的形式將它們包含在筆記本中，也可以使用 `IPython.display` 模組以程式設計方式予以產生。

**資料：** 您可以透過程式設計方式，將資料從 Azure Cosmos 容器或查詢的結果匯入 Jupyter Notebooks。 使用內建的魔術命令，來上傳或查詢 Azure Cosmos DB 中的資料。 

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure Cosmos DB 中的內建 Jupyter Notebooks，請參閱下列文章：

* [啟用 Azure Cosmos 帳戶中的筆記本](enable-notebooks.md)
* [使用 Python 筆記本功能和命令](use-python-notebook-features-and-commands.md)
* [使用 C# 筆記本功能和命令](use-csharp-notebook-features-and-commands.md)
