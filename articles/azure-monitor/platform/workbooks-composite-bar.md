---
title: Azure 監視器活頁簿複合條形轉譯器
description: 瞭解所有 Azure 監視器活頁簿複合橫條圖轉譯器視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 80846ecb1ad48d9f8ba49f0025772b4e131c23e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776352"
---
# <a name="composite-bar-renderer"></a>複合 bar 轉譯器

活頁簿可讓您使用複合列（由多個橫條組成的橫條）來呈現資料。

下圖顯示資料庫狀態的複合橫條圖，代表有多少部伺服器處於線上、離線和復原狀態。

![資料庫狀態的複合橫條圖螢幕擷取畫面。](./media/workbooks-composite-bar/database-status.png)

格線、圖格和圖形視覺效果支援複合列轉譯器。

## <a name="adding-composite-bar-renderer"></a>加入複合 bar 轉譯器

1. 選取 [ *編輯* ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ *加入* ]，然後按一下 [ *加入查詢*]。
3. 將 [ *資料來源* ] 設定為 [JSON]，並將 *視覺效果* 設定為 [方格]。
4. 新增下列 JSON 資料。

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. 執行查詢。
6. 選取 [資料 **行設定** ] 以開啟設定。
7. 從資料 *行* 中選取 [總計]，然後選擇 [複合 *列*] 以用於資料行轉譯器。
8. 在 [ *複合*列設定] 下設定下列設定。

| 資料行名稱 | Color        |
|-------------|--------------|
| 線上      | 綠色        |
| 復原  | 黃色       |
| 離線     | 紅色 (明亮)  |

9. 新增標籤：`["online"] of ["total"] are healthy`
10. 在 [線上]、[離線] 和 [復原] 的 [資料行設定] 中，您可以將資料行轉譯器設定為 [隱藏] (選擇性) 
11. 選取頂端的 *標籤* ，然後將總計資料行的標籤更新為「資料庫狀態」 (選擇性) 。
12. 選取 [ **Apply**套用]

複合橫條圖的設定看起來會像下面的螢幕擷取畫面：

![具有上述設定之複合橫條圖資料行設定的螢幕擷取畫面。](./media/workbooks-composite-bar/composite-bar-settings.png)

具有上述設定的複合列：

![複合橫條圖的螢幕擷取畫面。](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>複合橫條圖設定

選取 [資料行名稱] 和對應的色彩，將該色彩中的該資料行轉譯為複合橫條圖的一部分。 您可以上下插入、刪除和移動資料列。

### <a name="label"></a>標籤

複合橫條圖標籤會顯示在複合列的上方。 您可以混合使用靜態文字、資料行和參數。  如果標籤是空的，則目前資料行的值會顯示為標籤。 在先前的範例中，如果我們將標籤欄位保留為黑色，則會顯示總計資料行的值。

使用來參考資料行 `["columnName"]` 。

請使用來參考參數 `{paramName}` 。

資料行名稱和參數名稱都區分大小寫。 您也可以選取 [將此專案設為連結]，然後新增連結設定，將標籤設為連結。

### <a name="aggregation"></a>彙總

匯總適用于依視覺效果的樹狀結構/群組。 群組資料列之資料行的資料是由該資料行的匯總集所決定。 有三種類型的匯總適用于複合橫條圖： None、Sum 和 Inherit。

若要加入群組依據設定：

1. 在 [資料行設定] 中，移至您想要新增設定的資料行。
2. 在 [樹狀結構 */群組依據] 底下的設定**下，選取*[**群組依據**]
3. 選取您想要分組依據的欄位。

![[群組依據] 設定的螢幕擷取畫面。](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>無

無匯總表示群組資料列的資料行沒有顯示任何結果。

![具有無匯總的複合橫條圖螢幕擷取畫面。](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

如果匯總設定為 Sum，則群組資料列中的資料行會使用用來呈現資料行的總和來顯示覆合列。 標籤也會使用其中所參考資料行的總和。

在下列範例中，線上、離線和復原都有設定的最大匯總，而且總計資料行的匯總為 sum。

![具有總和匯總的複合橫條圖螢幕擷取畫面。](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>繼承

如果 [匯總] 設定為 [繼承]，則群組資料列中的資料行會針對用來呈現資料行的資料行，使用匯總集來顯示覆合列。 標籤中使用的資料行也會使用使用者所設定的匯總。 如果目前的資料行轉譯器是複合列，且 refereed 在標籤 (例如上述範例中的「總計」) ，則會使用 sum 作為該資料行的匯總。

在下列範例中，online、offline 和復原都有設定的最大匯總，而且 total 資料行的匯總是繼承的。

![具有繼承匯總的複合橫條圖螢幕擷取畫面。](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>排序

若為方格視覺效果，使用複合列轉譯器來排序資料行的資料列，會根據用來動態轉譯複合 bar 電腦之資料行總和的值來運作。 在先前的範例中，用於排序的值是該特定資料列的 online、復原和離線資料行的總和。

## <a name="tiles-visualization"></a>磚視覺效果

1. 選取 [ **加入** 並 *加入查詢*]。
2. 將資料來源變更為 JSON：輸入 [先前範例](#adding-composite-bar-renderer)中的資料。
3. 將視覺效果變更為 *磚*。
4. 執行查詢。
5. 選取 [ **磚設定**]。
6. 在 [並排顯示欄位] 中選取 [ *左方* ]。
7. 在 [ *欄位設定*] 下方輸入下列設定。
    1. 使用資料行： "server"。
    2. 資料行轉譯器： "Text"。
8. 選取 [並排顯示] 欄位中的 *底部* 。
9. 在 [ *欄位設定*] 下方輸入下列設定。
    1. 使用資料行：「總計」。
    2. 資料行轉譯器：「複合橫條」。
    3. 在 [複合列設定] 下方輸入設定下列設定。

    | 資料行名稱 | Color        |
    |-------------|--------------|
    | 線上      | 綠色        |
    | 復原  | 黃色       |
    | 離線     | 紅色 (明亮)  |

    4. 新增標籤： `["online"] of ["total"] are healthy` 。
10. 選取 [套用]。

磚的複合橫條圖設定：

![上述設定的複合橫條圖磚設定螢幕擷取畫面。](./media/workbooks-composite-bar/tiles-settings.png)

具有上述設定之磚的複合橫條圖看起來會像這樣：

![複合橫條圖格的螢幕擷取畫面。](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>圖形視覺效果

若要製作圖形視覺效果的複合條形轉譯器 (輸入 Hive 叢集) ，請遵循下列指示。

1. 選取 [ **加入** 並 *加入查詢*]。
2. 將資料來源變更為 JSON：輸入 [先前範例](#adding-composite-bar-renderer)中的資料。
3. 將視覺效果變更為 *圖形*。
4. 執行查詢。
5. 選取 [ **圖形設定]**。
6. 選取 [在節點格式設定中設定 *中心內容* ]。
7. 在 [ *欄位設定*] 下方輸入下列設定。
    1. 使用資料行：「總計」。
    2. 資料行轉譯器：「複合橫條」。
    3. 在 [ *複合列設定*] 下方輸入下列設定。

    |資料行名稱  |     Color    |
    |-------------|--------------|
    | 線上      | 綠色        |
    | 復原  | 黃色       |
    | 離線     | 紅色 (明亮)  |

   4. 新增標籤： `["online"] of ["total"] are healthy` 。
9. 在 [ *版面配置設定*] 下，輸入下列設定。
    1. 圖形類型： **Hive**叢集。
    2. 節點識別碼選取： "server"。
    3. 依欄位分組：「無」。
    4. 節點大小：100。
    5. 六邊形：5之間的邊界。
    6. 著色類型類型： **無**。
1. 選取 [套用]。
    
圖形的複合橫條圖設定：

![具有上述設定之複合橫條圖設定的螢幕擷取畫面。](./media/workbooks-composite-bar/graphs-settings.png)

具有上述設定之圖形的複合橫條圖看起來會像這樣：

![具有 hive 叢集之複合橫條圖的螢幕擷取畫面。](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>後續步驟

* 使用 Azure Resource Manager[部署](workbooks-automate.md)活頁簿。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
