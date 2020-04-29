---
title: 建立用戶端效能追蹤
description: 使用 WPF 進行用戶端效能分析的最佳做法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681307"
---
# <a name="create-client-side-performance-traces"></a>建立用戶端效能追蹤

有許多原因會導致 Azure 遠端轉譯的效能可能不會如預期般正常。 除了雲端伺服器上的純轉譯效能之外，尤其是網路連線的品質對體驗有重大影響。 若要分析伺服器的效能，請參閱[伺服器端效能查詢](../overview/features/performance-queries.md)一章。

本章著重于如何透過*效能追蹤*來識別潛在的用戶端瓶頸。

## <a name="getting-started"></a>開始使用

如果您是 Windows 效能追蹤功能的新手，本節將提及最基本的詞彙和應用程式，讓您開始著手。

### <a name="installation"></a>安裝

用來追蹤 ARR 的應用程式是一般用途的工具，可用於所有 Windows 開發。 它們是透過[Windows 效能工具](https://docs.microsoft.com/windows-hardware/test/wpt/)組提供。 若要取得此工具組，請下載[Windows 評定及部署套件](https://docs.microsoft.com/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>詞彙

搜尋效能追蹤的相關資訊時，您將無法避免一系列的詞彙。 最重要的是：

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW**代表[ **W**indows 的**E**通風管**T**比賽](https://docs.microsoft.com/windows/win32/etw/about-event-tracing)。 這只是 Windows 內建的有效率核心層級追蹤設備的首要名稱。 這稱為「*事件*追蹤」，因為支援 ETW 的應用程式會發出事件給記錄檔動作，有助於追蹤效能問題。 根據預設，作業系統已經發出磁片存取、工作參數等專案的事件。 像 ARR 的應用程式會另外發出自訂事件，例如有關已捨棄的畫面、網路延遲等。

**ETL**代表了**E**通風管**T**競賽**L**ogging。 這只是表示已收集（記錄）追蹤，因此通常會當做儲存追蹤資料之檔案的副檔名使用。 因此當您進行追蹤時，通常會有\*.etl 檔案。

**WPR**代表[ **W**indows **P**效能**R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) ，而是啟動和停止記錄事件追蹤的應用程式名稱。 WPR 會採用設定檔（\*. wprp）來設定要記錄的確切事件。 `wprp`這類檔案會隨 ARR SDK 一起提供。 在桌上型電腦上執行追蹤時，您可以直接啟動 WPR。 在 HoloLens 上進行追蹤時，您通常會改為流覽 web 介面。

**WPA**代表[ **W**indows **P**效能**A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) ，是 GUI 應用程式的名稱，用來開啟\*.etl 檔案並透過資料進行篩來識別效能問題。 WPA 可讓您依各種準則排序資料、以數種方式顯示資料、深入瞭解詳細資訊，以及將資訊相互關聯。

雖然可以在任何 Windows 裝置（本機電腦、HoloLens、雲端伺服器等）上建立 ETL 追蹤，但它們通常會儲存到磁片，並在桌上型電腦上使用 WPA 進行分析。 ETL 檔案可以傳送給其他開發人員，讓他們可以看到。 不過，請注意敏感性資訊（例如檔案路徑和 IP 位址）可能會在 ETL 追蹤中捕捉。 您可以透過兩種方式來使用 ETW：記錄追蹤或分析追蹤。 記錄追蹤是直接的，而且需要最少的設定。 另一方面，分析追蹤需要對 WPA 工具和您要調查的問題有深刻的瞭解。 下面將提供學習 WPA 的一般資料，以及如何解讀 ARR 特定追蹤的指導方針。

## <a name="recording-a-trace-on-a-local-pc"></a>在本機電腦上記錄追蹤

若要識別 ARR 效能問題，您應該想要直接在 HoloLens 上執行追蹤，因為這是取得真正效能特性之快照集的唯一方式。 不過，如果您特別想要在沒有 HoloLens 效能限制的情況下執行追蹤，或只是想要瞭解如何使用 WPA 而不需要實際的追蹤，以下是如何執行此動作的方法。

### <a name="wpr-configuration"></a>WPR 設定

1. 從 [*開始] 功能表*啟動[Windows 效能錄製](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)器。
1. 展開**更多選項**
1. 按一下 [**新增設定檔 ...** ]
1. 選取 AzureRemoteRenderingNetworkProfiling 檔案*wprp*。 您可以在 [*工具]/[ETLProfiles*] 下的 [ARR SDK] 中找到此檔案。
   設定檔現在會列在 [*自訂度量*] 底下的 [WPR] 中。 請確定它是唯一啟用的設定檔。
1. 展開*第一個層級*分級：
    * 如果您只想要捕獲 ARR 網路事件的快速追蹤，請**停**用此選項。
    * 如果您需要將 ARR 網路事件與其他系統特性（例如 CPU 或記憶體使用量）相互關聯，請**啟用**此選項。
    * 如果您啟用此選項，則追蹤最可能是大小為數 gb，而在 WPA 中儲存和開啟的時間較長。

之後，您的 WPR 設定看起來應該像這樣：

![WPR 設定](./media/wpr.png)

### <a name="recording"></a>記錄

按一下 [**啟動**] 開始錄製追蹤。 您隨時都可以開始和停止錄製;在這麼做之前，您不需要關閉應用程式。 如您所見，您不需要指定要追蹤哪一個應用程式，因為 ETW 一律會記錄整個系統的追蹤。 `wprp`檔案會指定要記錄的事件種類。

按一下 [**儲存**] 以停止錄製，並指定要儲存 ETL 檔案的位置。

您現在有一個 ETL 檔案，可以直接在 WPA 中開啟，或傳送給其他人。

## <a name="recording-a-trace-on-a-hololens"></a>記錄 HoloLens 上的追蹤

若要記錄 HoloLens 的追蹤，請啟動您的裝置，並在瀏覽器中輸入其 IP 位址，以開啟*裝置入口網站*。

![裝置入口網站](./media/wpr-hl.png)

1. 在左側，流覽至 [*效能] > [效能追蹤*]。
1. 選取**自訂設定檔**
1. 按一下 **[流覽]**
1. 選取 AzureRemoteRenderingNetworkProfiling 檔案*wprp*。 您可以在 [*工具]/[ETLProfiles*] 下的 [ARR SDK] 中找到此檔案。
1. 按一下 [**開始追蹤**]
1. HoloLens 現在正在錄製追蹤。 請務必觸發您想要調查的效能問題。 然後按一下 [**停止追蹤**]。
1. 追蹤將會列在網頁底部。 按一下右手邊的磁片圖示以下載 ETL 檔案。

您現在有一個 ETL 檔案，可以直接在 WPA 中開啟，或傳送給其他人。

## <a name="analyzing-traces-with-wpa"></a>使用 WPA 分析追蹤

### <a name="wpa-basics"></a>WPA 基本概念

Windows Performance Analyzer 是開啟 ETL 檔案和檢查追蹤的標準工具。 說明 WPA 的運作方式不在本文的範圍內。 若要開始使用，請參閱下列資源：

* 觀看[簡介](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer)影片以瞭解第一個總覽。
* WPA 本身具有 [*消費者入門*] 索引標籤，其中會說明常見的步驟。 請查看可用的主題。 特別是在「查看資料」下，您會取得如何為特定資料建立圖形的快速簡介。
* [此網站上](https://randomascii.wordpress.com/2015/09/24/etw-central/)有絕佳的資訊，不過並非所有的都與初學者相關。

### <a name="graphing-data"></a>圖形資料

若要開始使用 ARR 追蹤，您可以瞭解下列幾個部分。

![效能圖表](./media/wpa-graph.png)

上圖顯示追蹤資料的資料表，以及相同資料的圖形標記法。

在底部的表格中，記下黃色（黃金）列和藍色列。 您可以拖曳這些橫條，並將它們放在任何位置。

**黃色橫條左邊**的所有資料行都會被視為**按鍵**。 索引鍵是用來結構在左上角視窗中的樹狀目錄。 這裡有兩個索引*鍵*資料行：「提供者名稱」和「工作名稱」。 因此，左上角視窗中的樹狀結構是深度的兩個層級。 如果您重新排列資料行，或在索引鍵區域中加入或移除資料行，樹狀檢視中的結構就會變更。

**藍色列右邊**的資料行會用於在右上方視窗中**顯示的圖形**。 在大部分的情況下，只會使用第一個資料行，但某些圖形模式需要多個資料行。 若要讓折線圖能夠正常執行，必須設定該資料行上的*匯總模式*。 使用「Avg」或「Max」。 當圖元涵蓋具有多個事件的範圍時，會使用匯總模式來判斷給定圖元的圖形值。 這可以藉由將匯總設定為「總和」，然後再放大和縮小來觀察。

中間的資料行沒有特殊意義。

![事件檢視](./media/wpa-event-view.png)

在 [*一般事件] 視圖編輯器*中，您可以設定要顯示的所有資料行、匯總模式、排序，以及使用哪些資料行做為索引鍵或圖形。 在上述範例中，已啟用**欄位 2** ，而欄位 3-6 已停用。 [欄位 2] 通常是 ETW 事件的第一個*自訂資料*欄位，因此是代表一些網路等待時間值的 ARR "FrameStatistics" 事件。 啟用其他「欄位」資料行，以查看此事件的進一步值。

### <a name="presets"></a>預設值

若要正確分析追蹤，您必須找出您自己的工作流程和慣用的資料顯示。 不過，若要能夠快速流覽 ARR 特定的事件，我們在資料夾*Tools/ETLProfiles*中包含 Windows 軟體保護平台佈建檔和預設檔案。 若要載入完整的設定檔，請從 WPA 功能表列選取 [*設定檔] > 套用 ...* ]，或開啟 [*我*的預設值] 面板（*視窗 > 我*的預設值），然後選取 [匯*入*]。 前者會設定完整的 WPA 設定，如下圖所示。 後者只會為可用的各種視圖設定提供預設值，並可讓您快速開啟視圖來查看特定的 ARR 事件資料。

![預設值](./media/wpa-arr-trace.png)

上圖顯示各種 ARR 特定事件的視圖，以及整體 CPU 使用率的觀點。

## <a name="next-steps"></a>後續步驟

* [伺服器端效能查詢](../overview/features/performance-queries.md)
