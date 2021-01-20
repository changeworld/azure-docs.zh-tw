---
title: 教學課程：開始探索 Synapse 知識中心
description: 在本教學課程中，您將了解如何使用 Synapse 知識中心。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209197"
---
# <a name="explore-the-synapse-knowledge-center"></a>探索 Synapse 知識中心

在本教學課程中，您將了解如何使用 Synapse Studio 知識中心。

## <a name="getting-to-the-knowledge-center"></a>進入知識中心

有兩種方式可找到 Synapse Studio 中的知識中心：

  1. 在 [首頁] 中樞的頁面右上方，按一下 [學習]。
  2. 在頂端的功能表列上，按一下 [？] 然後 **知識中心**。

挑選其中一個方法並開啟 [知識中心]。

## <a name="overview"></a>概觀

**知識中心** 可讓您執行三項動作：
* **立即使用範例**。 如果您需要 Synapse 運作方式的快速範例，請選擇此選項。
* **瀏覽資源庫**。 此選項可讓您連結資料集範例，並以 SQL 指令碼、筆記本和管線的形式新增程式碼範例。
* **導覽 Synapse Studio**。 此選項會帶您快速瀏覽 Synapse Studio 的基本組件。 如果您從未使用過 Synapse Studio，此選項會很實用。

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>使用無伺服器 SQL 集區探索 Blob 儲存體

1. 移至 **知識中心**，按一下 [ **立即使用範例**]。
1. 選取 **[使用 SQL 查詢資料**]。
1. 按一下 [ **使用範例**]。
1. 將會開啟新的範例 SQL 腳本。
1. 滾動至第一個查詢 (第28行到 32) ，然後選取查詢文字。
1. 按一下 [執行]。 它只會執行您所選取的程式碼。

## <a name="loading-more-nyc-taxi-data"></a>載入更多 NYC 計程車資料
1. 移至 **知識中心**，按一下 **[流覽資源庫]**。
1. 選取頂端的 [ **SQL 腳本** ] 索引標籤。
1. 選取 **[載入紐約計程車資料集** 資料內嵌範例]，然後按一下 [ **繼續**]。
1. 在 **[SQL 集** 區] 下，選擇 [ **選取現有的集** 區] 並選取 [ **SQLPOOL1**]，然後選取您稍早建立的 **SQLPOOL1** 資料庫。
1. 按一下 [ **開啟腳本**]。
1. 將會開啟新的範例 SQL 腳本。
1. 按一下 [執行] 
1. 這會為所有 NYC 計程車資料建立數個資料表，並使用 T-SQL COPY 命令來載入這些資料表。 如果您已在先前的快速入門步驟中建立這些資料表，請選取並只執行程式碼，以針對不存在的資料表建立和複製。

    > [!NOTE] 
    > 對專用 SQL 集區 (先前稱為 SQL DW) 使用 SQL 指令碼的範例庫時，您將只能使用現有的專用 SQL 集區 (先前稱為 SQL DW)。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](get-started.md)
* [建立工作區](quickstart-create-workspace.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)
