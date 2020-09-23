---
title: Azure 監視器建立參數的活頁簿
description: 瞭解參數如何讓活頁簿作者收集取用者的輸入，並在活頁簿的其他部分中參考。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8be7dd170d5e4e3bddb09bc1b163fba7a841a6b7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984566"
---
# <a name="workbook-parameters"></a>活頁簿參數

參數可讓活頁簿作者收集取用者的輸入，並在活頁簿的其他部分中參考它，通常是為了設定結果集的範圍或設定正確的視覺效果。 它是一項重要功能，可讓作者建立互動式報表和體驗。 

活頁簿可讓您控制如何向取用者顯示您的參數控制項-文字方塊與下拉式清單、單一與多重選取、文字、JSON、KQL 或 Azure Resource Graph 等的值等等。  

支援的參數類型包括：
* [時間](workbooks-time.md) -允許使用者從預先填入的時間範圍中選取，或選取自訂範圍
* [下拉式](workbooks-dropdowns.md) 清單-允許使用者從值或一組值進行選取
* [文字](workbooks-text.md) -允許使用者輸入任意文字
* [資源](workbooks-resources.md) -可讓使用者選取一或多個 Azure 資源
* [訂](workbooks-resources.md) 用帳戶-可讓使用者選取一或多個 Azure 訂用帳戶資源
* 資源類型-可讓使用者選取一或多個 Azure 資源類型值
* 位置-可讓使用者選取一或多個 Azure 位置值

這些參數值可以透過系結或值展開，在活頁簿的其他部分中參考。

## <a name="creating-a-parameter"></a>建立參數
1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `TimeRange` * (請注意，參數__名稱__**不能**包含空格或特殊字元) *
    2. 顯示名稱： `Time Range` * (不過，__顯示名稱__可以包含空格、特殊字元、表情等 ) *  
    2. 參數類型： `Time range picker`
    3. 必填： `checked`
    4. 可用的時間範圍：過去一小時、過去12小時、過去24小時、過去48小時、過去3天、過去7天，並允許自訂時間範圍選取
5. 從工具列中選擇 [儲存] 以建立參數。

   ![顯示建立時間範圍參數的影像](./media/workbooks-parameters/time-settings.png)

這是活頁簿在讀取模式中的外觀，以「框之間」樣式。

   ![以讀取模式顯示時間範圍參數的影像](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>參考參數
### <a name="via-bindings"></a>Via 系結
1. 將查詢控制項新增至活頁簿，然後選取 Application Insights 資源。
2. 開啟 [ _時間範圍_ ] 下拉式清單，然後 `Time Range` 從底部的 [參數] 區段中選取選項。
3. 這會將時間範圍參數系結至圖表的時間範圍。 範例查詢的時間範圍現在是過去24小時。
4. 執行查詢以查看結果

    ![顯示透過系結參考之時間範圍參數的影像](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 將查詢控制項新增至活頁簿，然後選取 Application Insights 資源。
2. 在 KQL 中，使用參數輸入時間範圍篩選準則： `| where timestamp {TimeRange}`
3. 這會將查詢評估時間展開為 `| where timestamp > ago(1d)` ，這是參數的時間範圍值。
4. 執行查詢以查看結果

    ![顯示 KQL 中所參考時間範圍的影像](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>在文字中 
1. 將文字控制項加入至活頁簿。
2. 在 markdown 中，輸入 `The chosen time range is {TimeRange:label}`
3. 選擇 [_完成編輯_]
4. 文字控制項將顯示文字： _選擇的時間範圍是過去24小時_

## <a name="parameter-options"></a>參數選項
_In Text_區段使用參數的， `label` 而不是其值。 參數會根據其類型來公開各種這類選項，例如時間範圍選擇器允許值、標籤、查詢、開始、結束和細微性。

`Previews`您可以使用 [_編輯參數_] 窗格的區段來查看參數的擴充選項：

![顯示時間範圍參數選項的影像](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>下一步

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
