---
title: 管理對應的資料流程圖
description: 如何有效地管理和編輯對應的資料流程圖
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590455"
---
# <a name="managing-the-mapping-data-flow-graph"></a>管理對應的資料流程圖

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

對應資料流程是使用設計介面（也就是資料流程圖）來撰寫。 在圖形中，轉換邏輯是由左至右建立的，而且會由上而下新增其他資料串流。 若要加入新的轉換，請選取現有轉換右下角的加號。

![畫布](media/data-flow/canvas-2.png)

當您的資料流程變得更複雜時，請使用下列機制有效地流覽和管理資料流程圖形。 

## <a name="moving-transformations"></a>移動轉換

在對應資料流程中，一組連接的轉換邏輯稱為 **資料流程**。 [內送 **資料流程** ] 欄位會指出目前轉換的資料串流。 每個轉換都有一或兩個傳入的資料流程，視其函式而定，並代表輸出資料流程。 傳入資料流程的輸出架構會決定目前轉換可以參考的資料行中繼資料。

![移動節點](media/data-flow/move-nodes.png "移動節點")

與管線畫布不同的是，不會使用拖放模型來編輯資料流程轉換。 若要變更內送資料流程或「移動」轉換，請從 [連 **入資料流程** ] 下拉式清單中選擇不同的值。 當您這樣做時，所有下游轉換都會隨著編輯過的轉換一起移動。 圖形會自動更新以顯示新的邏輯流程。 如果您將內送資料流程變更為已經有下游轉換的轉換，則會建立新的分支或平行資料流程。 深入瞭解 [對應資料流程中的新分支](data-flow-new-branch.md)。

## <a name="hide-graph-and-show-graph"></a>隱藏圖形並顯示圖形

當您編輯轉換時，可以展開 [設定] 面板以佔用整個畫布，隱藏圖形。 按一下位於畫布右側的向上箭號 v。

![隱藏圖形](media/data-flow/hide-graph.png "隱藏圖形")

隱藏圖形時，您可以按一下 **[下一步]** 或 [ **上** 一步]，在資料流程內的轉換之間移動。 按一下向下箭號箭號以顯示圖形。

![顯示圖形](media/data-flow/show-graph.png "顯示圖形")

## <a name="searching-for-transformations"></a>搜尋轉換

若要在圖形中快速尋找轉換，請按一下 [縮放] 設定上方的 [ **搜尋** ] 圖示。

![螢幕擷取畫面：顯示 [搜尋] 按鈕。](media/data-flow/search-1.png "搜尋圖表")

您可以依轉換名稱或描述來搜尋，以找出轉換。

![螢幕擷取畫面：顯示 [搜尋] 文字方塊。](media/data-flow/search-2.png "搜尋圖表")

## <a name="hide-reference-nodes"></a>隱藏參考節點

如果您的資料流程具有任何聯結、查閱、存在或等位轉換，資料流程會顯示所有傳入資料流程的參考節點。 如果您想要將所花費的垂直空間量降至最低，您可以最小化您的參考節點。 若要這樣做，請以滑鼠右鍵按一下畫布，然後選取 [ **隱藏參考節點**]。

![隱藏參考節點](media/data-flow/hide-reference-nodes.png "隱藏參考節點")

## <a name="next-steps"></a>後續步驟

完成資料流程邏輯之後，請開啟 [偵測 [模式]](concepts-data-flow-debug-mode.md) ，並在資料預覽中進行測試。
