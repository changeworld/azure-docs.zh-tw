---
title: 建立用戶端效能追蹤
description: 使用 WPF 進行用戶端效能分析的最佳作法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204090"
---
# <a name="create-client-side-performance-traces"></a>建立用戶端效能追蹤

有許多原因會導致 Azure 遠端轉譯的效能可能不適合您所需的效能。 除了雲端伺服器上的單純轉譯效能，特別是網路連線品質對於體驗有顯著的影響。 若要分析伺服器的效能，請參閱 [伺服器端效能查詢](../overview/features/performance-queries.md)章節。

本章著重于如何透過找出潛在客戶端的瓶頸 *:::no-loc text="performance traces":::* 。

## <a name="getting-started"></a>開始使用

如果您不熟悉 Windows :::no-loc text="performance tracing"::: 功能，本章節將提及最基本的詞彙和應用程式，讓您開始著手。

### <a name="installation"></a>安裝

用來追蹤 ARR 的應用程式是可用於所有 Windows 開發的一般用途工具。 它們是透過 [Windows 效能工具](/windows-hardware/test/wpt/)組所提供。 若要取得此工具組，請下載 [Windows 評定及部署套件](/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>詞彙

在搜尋效能追蹤的相關資訊時，您不一定會遇到各種不同的詞彙。 最重要的是：

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW**代表[ **W**>indows 的**E**通風管**T**賽車](/windows/win32/etw/about-event-tracing)。 這只是 Windows 內建的有效率核心層級追蹤設備的整體名稱。 這稱為「 *事件* 追蹤」，因為支援 ETW 的應用程式會發出事件，以記錄可能有助於追蹤效能問題的動作。 根據預設，作業系統已針對磁片存取、工作切換等專案發出事件。 ARR 之類的應用程式會額外發出自訂事件，例如，已卸載的框架、網路延遲等等。

**ETL** 代表 **E**通風管 **T**競爭 **L**ogging。 這只是表示追蹤已 (記錄) 收集，因此通常做為儲存追蹤資料之檔案的副檔名。 因此，當您進行追蹤時，通常會有 .etl 檔。 \*

**WPR**代表[ **W**>indows **P**效能**R**ecorder](/windows-hardware/test/wpt/windows-performance-recorder) ，這是啟動及停止記錄事件追蹤的應用程式名稱。 WPR 會使用設定檔 (\* . wprp) ，設定要記錄的確切事件。 `wprp`ARR SDK 會提供這類檔案。 在桌上型電腦上執行追蹤時，您可以直接啟動 WPR。 在 HoloLens 上進行追蹤時，您通常會改為流覽 web 介面。

**WPA**代表[ **W**>indows **P**效能 nalyzer **A** ](/windows-hardware/test/wpt/windows-performance-analyzer) ，它是 GUI 應用程式的名稱，用來開啟 \* .etl 檔案並透過資料進行流覽，以找出效能問題。 WPA 可讓您依各種準則來排序資料、以數種方式顯示資料、深入探討詳細資料，以及相互關聯資訊。

雖然可以在任何 Windows 裝置上建立 ETL 追蹤 (本機電腦、HoloLens、雲端伺服器等 ) ，但它們通常會儲存至磁片，並在桌上型電腦上使用 WPA 進行分析。 ETL 檔案可以傳送給其他開發人員，讓他們有外觀。 不過請注意，您可以在 ETL 追蹤中捕捉敏感資訊，例如檔案路徑和 IP 位址。 您可以透過兩種方式使用 ETW：記錄追蹤或分析追蹤。 錄製追蹤是很簡單的，而且需要進行基本設定。 另一方面，分析追蹤需要對 WPA 工具和您正在調查的問題有相當的瞭解。 以下將提供學習 WPA 的一般材質，以及如何解讀 ARR 特定追蹤的指導方針。

## <a name="recording-a-trace-on-a-local-pc"></a>在本機電腦上記錄追蹤

若要找出 ARR 效能問題，您應該直接在 HoloLens 上進行追蹤，因為這是取得真正效能特性之快照集的唯一方式。 但是，如果您特別想要執行不含 HoloLens 效能限制的追蹤，或只想要瞭解如何使用 WPA，而不需要實際的追蹤，則以下是如何執行這項操作的方法。

### <a name="wpr-configuration"></a>WPR 設定

1. [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder)從 [開始]*功能表*啟動。
1. 展開 **更多選項**
1. 按一下 [**新增設定檔**]。
1. 選取 *AzureRemoteRenderingNetworkProfiling wprp*。 您可以在 [ *工具/ETLProfiles*] 下的 ARR SDK 中找到此檔案。
   設定檔現在會列在 WPR 的 [ *自訂度量*] 下。 請確定它是唯一啟用的設定檔。
1. 展開 *第一個層級*分級：
    * 如果您只想要捕捉 ARR 網路事件的快速追蹤，請 **停** 用此選項。
    * 如果您需要讓 ARR 網路事件與其他系統特性（例如 CPU 或記憶體使用量）相互關聯，請 **啟用** 此選項。
    * 如果您啟用此選項，則追蹤最多可能會有多 gb 的大小，且需要較長的時間來儲存並在 WPA 中開啟。

之後，您的 WPR 設定看起來應該像這樣：

![WPR 設定](./media/wpr.png)

### <a name="recording"></a>記錄

按一下 [ **開始** ] 以開始記錄追蹤。 您可以隨時啟動及停止錄製;在這麼做之前，您不需要關閉應用程式。 您可以看到，您不需要指定要追蹤的應用程式，因為 ETW 一律會為整個系統記錄追蹤。 檔案會 `wprp` 指定要記錄的事件種類。

按一下 [ **儲存** ] 以停止錄製並指定儲存 ETL 檔案的位置。

您現在有一個 ETL 檔案，您可以直接在 WPA 中開啟，或傳送給其他人。

## <a name="recording-a-trace-on-a-hololens"></a>在 HoloLens 上錄製追蹤

若要在 HoloLens 上記錄追蹤，請啟動您的裝置，並在瀏覽器中輸入其 IP 位址以開啟 *裝置入口網站*。

![裝置入口網站](./media/wpr-hl.png)

1. 在左側流覽至 [ *效能] > 效能追蹤*。
1. 選取 **自訂設定檔**
1. 點擊 **:::no-loc text="Browse...":::**
1. 選取 *AzureRemoteRenderingNetworkProfiling wprp*。 您可以在 [ *工具/ETLProfiles*] 下的 ARR SDK 中找到此檔案。
1. 按一下 [**開始追蹤**]
1. HoloLens 現在正在記錄追蹤。 請務必觸發您要調查的效能問題。 然後按一下 [ **停止追蹤**]。
1. 追蹤將會列在網頁的底部。 按一下右手邊的磁片圖示以下載 ETL 檔案。

您現在有一個 ETL 檔案，您可以直接在 WPA 中開啟，或傳送給其他人。

## <a name="analyzing-traces-with-wpa"></a>使用 WPA 分析追蹤

### <a name="wpa-basics"></a>WPA 基本概念

Windows Performance Analyzer 是開啟 ETL 檔案並檢查追蹤的標準工具。 說明 WPA 如何運作不在本文的討論範圍內。 若要開始使用，請參閱下列資源：

* 觀看 [簡介](/windows-hardware/test/wpt/windows-performance-analyzer) 影片，以取得首次總覽。
* WPA 本身有一個 *消費者入門* 索引標籤，其中說明一般步驟。 查看可用的主題。 尤其是在「查看資料」下，您會看到如何建立特定資料圖形的快速簡介。
* [本網站上](https://randomascii.wordpress.com/2015/09/24/etw-central/)有絕佳的資訊，但並非全部都與初學者相關。

### <a name="graphing-data"></a>圖形資料

若要開始使用 ARR 追蹤，您可以瞭解下列各項。

![效能圖表](./media/wpa-graph.png)

上圖顯示追蹤資料的資料表，以及相同資料的圖表表示。

在底部的表格中，注意黃色 (黃金) 列和藍色橫條。 您可以拖曳這些橫條，並將它們放在任何位置。

**黃色列左邊**的所有資料行都會被視為索引**鍵**。 索引鍵是用來在左上方視窗中結構樹狀結構。 這裡有兩個索引 *鍵* 資料行：「提供者名稱」和「工作名稱」。 因此，左上角視窗中的樹狀結構是深度的兩個層級。 如果您重新排列資料行，或從索引鍵區域中加入或移除資料行，樹狀檢視中的結構就會變更。

**藍色列右邊** 的資料行會用於右上方視窗中的 **圖形顯示** 。 大部分情況下，只會使用第一個資料行，但某些圖表模式需要多個資料行。 若要讓線條圖形正常運作，必須設定該資料行的 *匯總模式* 。 使用「平均」或「最大值」。 當圖元涵蓋具有多個事件的範圍時，匯總模式會用來判斷指定圖元的圖形值。 您可以將匯總設定為 ' Sum '，然後放大和縮小，以觀察這一點。

中間的資料行沒有特殊意義。

![事件檢視](./media/wpa-event-view.png)

在 [ *一般事件檢視編輯器* ] 中，您可以設定要顯示的所有資料行、匯總模式、排序，以及要使用哪些資料行做為索引鍵或圖形。 在上述範例中，已啟用 **欄位 2** ，並停用欄位 3-6。 欄位2通常是 ETW 事件的第一個 *自訂資料* 欄位，因此是用於 ARR "FrameStatistics" 事件，這代表一些網路等待時間值。 啟用其他「欄位」資料行，以查看此事件的其他值。

### <a name="presets"></a>預設值

若要適當地分析追蹤，您必須找出自己的工作流程和慣用的資料顯示。 不過，若要快速瞭解 ARR 特定事件，我們會在資料夾 *Tools/ETLProfiles*中包含 Windows 軟體保護平台佈建檔和預設檔案。 若要載入完整設定檔，請從 WPA 功能表列選取 [ *設定檔 > 套用 ...* ]，或開啟 [ *我* 的預設值] 面板 (*視窗 > 我* 的預設) ，然後選取 [匯 *入*]。 前者會設定完整的 WPA 設定，如下圖所示。 後者只會提供各種可用視圖設定的預設值，並可讓您快速開啟視圖來查看特定的 ARR 事件資料片段。

![預設值](./media/wpa-arr-trace.png)

上圖顯示各種 ARR 特定事件的視圖，以及整體 CPU 使用率的觀點。

## <a name="next-steps"></a>後續步驟

* [伺服器端效能查詢](../overview/features/performance-queries.md)