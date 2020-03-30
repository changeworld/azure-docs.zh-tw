---
title: Azure 監視器活頁簿創建參數
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658212"
---
# <a name="workbook-parameters"></a>活頁簿參數

參數允許活頁簿作者從消費者那裡收集輸入，並將其引用到活頁簿的其他部分中 - 通常用於限定結果集或設置正確的視覺物件。 它是一項關鍵功能，允許作者構建互動式報告和體驗。 

活頁簿允許您控制如何向消費者顯示參數控制項 - 文字方塊與下拉、單選與多選、文本值、JSON、KQL 或 Azure 資源圖等。  

支援的參數類型包括：
* [時間](workbooks-time.md)- 允許使用者從預填充的時間範圍中選擇或選擇自訂範圍
* [下拉](workbooks-dropdowns.md)- 允許使用者從值或一組值中選擇
* [文本](workbooks-text.md)- 允許使用者輸入任意文本
* [資源](workbooks-resources.md)- 允許使用者選擇一個或多個 Azure 資源
* [訂閱](workbooks-resources.md)- 允許使用者選擇一個或多個 Azure 訂閱資源
* 資源類型 - 允許使用者選擇一個或多個 Azure 資源類型值
* 位置 - 允許使用者選擇一個或多個 Azure 位置值

這些參數值可以通過綁定或值擴展在活頁簿的其他部分中引用。

## <a name="creating-a-parameter"></a>創建參數
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱`TimeRange`*：（請注意參數__名稱__**不能**包含空格或特殊字元）*
    2. 顯示名稱`Time Range`  *：（但是，__顯示名稱__可以包括空格、特殊字元、表情符號等）*
    2. 參數類型：`Time range picker`
    3. 必填：`checked`
    4. 可用時間範圍：最後一小時、最後 12 小時、最近 24 小時、最近 48 小時、最近 3 天、最近 7 天以及允許自訂時間範圍選擇
5. 從工具列中選擇"保存"以創建參數。

   ![顯示時間範圍參數創建的圖像](./media/workbooks-parameters/time-settings.png)

這就是活頁簿在閱讀模式下的樣子，在"Pills"樣式中。

   ![在讀取模式下顯示時間範圍參數的圖像](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>傳址參數
### <a name="via-bindings"></a>通過綁定
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 打開_時間範圍_下拉，並從底部的`Time Range`"參數"部分選擇該選項。
3. 這將時間範圍參數綁定到圖表的時間範圍。 依例查詢的時間範圍現在是最近 24 小時。
4. 執行查詢以查看結果

    ![顯示通過綁定引用的時間範圍參數的圖像](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 在 KQL 中，使用 參數輸入時間範圍篩選器：`| where timestamp {TimeRange}`
3. 這將在查詢評估時間上擴展為`| where timestamp > ago(1d)`，這是參數的時間範圍值。
4. 執行查詢以查看結果

    ![顯示 KQL 中引用的時間範圍的圖像](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向活頁簿添加文本控制項。
2. 在標記下，輸入`The chosen time range is {TimeRange:label}`
3. 選擇 _"已完成編輯"_
4. 文本控制項將顯示文本：_所選時間範圍為"過去 24 小時"_

## <a name="parameter-options"></a>參數選項
_"輸入文本"_ 部分使用`label`參數 而不是其值。 參數根據其類型公開各種此類選項 - 例如時間範圍選取器允許值、標籤、查詢、開始、結束和顆粒。

使用`Previews`_"編輯參數"_ 窗格的部分查看參數的擴展選項：

![顯示時間範圍參數選項的圖像](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
