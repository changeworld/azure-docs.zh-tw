---
title: 建立用戶端效能追蹤
description: 使用 WPF 進行用戶端效能分析的最佳做法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681307"
---
# <a name="create-client-side-performance-traces"></a>建立用戶端效能追蹤

Azure 遠端呈現的性能可能不如預期的原因有很多。 除了雲端伺服器上的純渲染性能外,尤其是網路連接的品質對體驗有重要影響。 要分析伺服器的性能,請參閱[伺服器端性能查詢一](../overview/features/performance-queries.md)章。

本章重點介紹如何通過*性能跟蹤*識別潛在的用戶端瓶頸。

## <a name="getting-started"></a>開始使用

如果您是 Windows 性能追蹤功能的新功能,本節將提及入門項和應用程式的最基本術語和應用程式。

### <a name="installation"></a>安裝

用於使用 ARR 進行追蹤的應用程式是可用於所有 Windows 開發的普通工具。 它們透過 Windows[效能工具套件提供](https://docs.microsoft.com/windows-hardware/test/wpt/)。 要取得此工具套件,請下載[Windows 評估及部署工具套件](https://docs.microsoft.com/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>詞彙

搜索有關性能跟蹤的資訊時,不可避免地會遇到一系列術語。 最重要的是:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW**代表[**E**通風**T**賽車**為W**的內多斯](https://docs.microsoft.com/windows/win32/etw/about-event-tracing)。 它只是 Windows 中內置的有效內核級跟蹤工具的總名稱。 它被稱為*事件*跟蹤,因為支援ETW的應用程式將發出事件來記錄可能有助於跟蹤性能問題的操作。 默認情況下,操作系統已為磁碟訪問、任務開關等事件發出事件。 ARR 等應用程式還會發出自定義事件,例如關於丟棄的幀、網路延遲等。

**ETL**代表**E**通風**T**賽車**L**奧格。 它只是意味著已收集(記錄)跟蹤,因此通常用作存儲跟蹤數據的檔的檔擴展名。 因此,當您執行跟蹤時,您通常會在之後有\*一個 .etl 檔。

**WPR**代表[**W**indows **P**erformance **R**ecorder,](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)是啟動和停止記錄事件追蹤的應用程式的名稱。 WPR 採用配置檔\*檔 (.wprp),用於配置要記錄的確切事件。 ARR `wprp` SDK 提供了此類檔。 在臺式 PC 上執行追蹤時,可以直接啟動 WPR。 在 HoloLens 上執行追蹤時,您通常會透過 Web 介面。

**WPA**代表[**W**indos **P**erformance **A**nalyzer,](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer)是用於打開\*.etl 檔和篩選數據以識別效能問題的 GUI 應用程式的名稱。 WPA 允許您按各種條件對數據進行排序、以多種方式顯示數據、深入瞭解詳細資訊以及關聯資訊。

雖然可以在任何 Windows 設備上(本地 PC、HoloLens、雲端伺服器等)創建 ETL 追蹤,但它們通常保存到磁碟,並在桌上型 PC 上使用 WPA 進行分析。 ETL 檔可以發送給其他開發人員,讓他們查看。 但請注意,敏感資訊(如檔路徑和IP位址)可能會在ETL跟蹤中捕獲。 您可以通過兩種方式使用 ETW:記錄跟蹤或分析跟蹤。 錄製跟蹤是直截了當的,需要最少的設置。 另一方面,分析跟蹤確實需要對 WPA 工具和您正在調查的問題有一個體面的理解。 下文將提供學習 WPA 的一般材料,以及如何解釋 ARR 特定跟蹤的指南。

## <a name="recording-a-trace-on-a-local-pc"></a>在本地 PC 上錄製追蹤

要識別 ARR 性能問題,您應該希望直接在 HoloLens 上執行追蹤,因為這是獲取真實性能特徵快照的唯一方法。 但是,如果您特別想要在沒有 HoloLens 性能限制的情況下執行追蹤,或者只想瞭解如何使用 WPA 並且不需要逼真的追蹤,下面是如何執行此操作。

### <a name="wpr-configuration"></a>WPR 設定

1. 從*開始選單*啟動[Windows 效能紀錄器](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)。
1. 展開**更多選項**
1. 單擊"**添加配置檔..."**
1. 選擇檔案*Azure 遠端呈現網路分析.wprp*。 您可以在*工具/ETL 設定檔*下的 ARR SDK 中找到此檔。
   該配置檔現在將在 WPR 下在*自定義測量*下列出。 請確保它是唯一啟用的配置檔。
1. 展開*第一個分階段*:
    * 如果只想捕獲 ARR 網路事件的快速追蹤,**請關閉**這個選項。
    * 如果需要將 ARR 網路事件與其他系統特徵(如 CPU 或記憶體使用方式)相關聯,則**啟用**此選項。
    * 如果啟用此選項,跟蹤很可能為多個 GB 大小,並且需要很長時間才能在 WPA 中保存和打開。

之後,WPR 配置應如下所示:

![WPR 設定](./media/wpr.png)

### <a name="recording"></a>記錄

按下 **「開始」** 開始錄製追蹤。 您可以隨時開始和停止錄製;在執行此操作之前,您無需關閉應用程式。 正如您所看到的,您不需要指定要跟蹤的應用程式,因為 ETW 將始終記錄整個系統的追蹤。 該檔`wprp`指定要記錄的事件類型。

按下 **「儲存**」停止錄製並指定將 ETL 檔案儲存的位置。

現在,您有一個 ETL 檔,可以直接在 WPA 中打開該檔,也可以發送給其他人。

## <a name="recording-a-trace-on-a-hololens"></a>在 HoloLens 上記錄追蹤

要在 HoloLens 上記錄追蹤,請啟動您的裝置並將其 IP 位址輸入瀏覽器以開啟*裝置門戶*。

![裝置入口網站](./media/wpr-hl.png)

1. 在左側,導航到*效能>效能追蹤*。
1. 選擇**自訂設定檔**
1. 單擊 **"流覽..."**
1. 選擇檔案*Azure 遠端呈現網路分析.wprp*。 您可以在*工具/ETL 設定檔*下的 ARR SDK 中找到此檔。
1. 點擊 **"開始跟蹤"**
1. HoloLens 正在記錄一個跟蹤。 請確保觸發要調查的性能問題。 然後按下 **「停止追蹤**」。
1. 跟蹤將列在網頁底部。 按一下右方的磁碟圖示以下載 ETL 檔。

現在,您有一個 ETL 檔,可以直接在 WPA 中打開該檔,也可以發送給其他人。

## <a name="analyzing-traces-with-wpa"></a>使用 WPA 分析追蹤

### <a name="wpa-basics"></a>WPA 基礎知識

Windows 性能分析器是打開 ETL 檔和檢查追蹤的標準工具。 對本文的 WPA 工作方式的解釋已不為範圍。 要開始,請查看以下資源:

* 觀看[介紹性視頻](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer),瞭解第一次概述。
* WPA 本身具有 *「入門」* 選項卡,它解釋了常見步驟。 查看可用主題。 特別是在"查看數據"下,您可以快速介紹如何為特定數據創建圖形。
* 有[優秀的資訊在這個網站上](https://randomascii.wordpress.com/2015/09/24/etw-central/),但是,不是所有它相關的初學者。

### <a name="graphing-data"></a>繪製資料圖形

要開始使用 ARR 跟蹤,以下部分是一個很好的瞭解。

![效能圖](./media/wpa-graph.png)

上圖顯示了跟蹤數據的表和相同數據的圖形表示形式。

在底部的表格中,注意黃色(金色)條和藍色條。 您可以拖動這些條形,並將其放置在任何位置。

**黃色條形左方的所有欄**都解釋為**鍵**。 鍵用於在左上角視窗中建構樹。 在這裏,我們有兩*個關鍵*列,"提供程式名稱"和"任務名稱"。 因此,左上角視窗中的樹結構是兩層深。 如果重新排序列或從關鍵區域添加或刪除列,則樹視圖中的結構將更改。

**藍色條右邊的欄**位 右上角視窗中的**圖形顯示**。 大多數時候只使用第一列,但某些圖形模式需要多列數據。 要讓線圖正常工作,必須設定此欄的*集合模式*。 使用"平均"或"最大值" 當圖元覆蓋具有多個事件的範圍時,聚合模式用於確定給定圖元處圖形的值。 這可以通過將聚合設置為"Sum",然後放大和縮小來觀察。

中間的列沒有特殊的含義。

![事件檢視](./media/wpa-event-view.png)

在 *「通用事件檢視編輯器*」中,可以設定要顯示的所有欄、聚合模式、排序以及哪些列用作鍵或用於繪製圖形。 在上面的範例中,**欄位 2**已啟用,欄位 3 - 6 已停用。 欄位 2 通常是 ETW 事件的第一個*自訂資料*欄位,因此對於表示某些網路延遲值的 ARR"FrameStatistics"事件而言。 啟用其他"欄位"列以查看此事件的進一步值。

### <a name="presets"></a>預設值

要正確分析跟蹤,您需要找出自己的工作流和首選數據顯示。 但是,為了能夠快速概覽特定於 ARR 的事件,我們在資料夾*工具/ ETL 配置檔*中包括 Windows 軟體保護平台配置檔和預設檔。 要載入完整設定檔,請從 WPA 選單列中選擇 *「應用設定檔 >」,* 或打開「*我的預設」* 面板(*視窗> 我的預設*)並選擇「*導入*」 。 前者將設置完整的 WPA 配置,如下圖所示。 後者將僅對可用的各種檢視配置進行預設,並允許您快速打開檢視以查看特定 ARR 事件資料段。

![預設值](./media/wpa-arr-trace.png)

上圖顯示了各種 ARR 特定事件的檢視以及總體 CPU 利用率的檢視。

## <a name="next-steps"></a>後續步驟

* [伺服器端效能查詢](../overview/features/performance-queries.md)
