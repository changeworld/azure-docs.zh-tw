---
title: 對應資料流程
description: Azure 資料工廠中的映射資料串流的概述
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 8420cba043e7fc9285b473b877f0ff10212e9fda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605321"
---
# <a name="what-are-mapping-data-flows"></a>什麼是對應資料流程？

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

映射資料流是在 Azure 數據工廠中直觀地設計的數據轉換。 數據流允許數據工程師無需編寫代碼即可開發圖形數據轉換邏輯。 生成的數據流作為使用橫向擴展的 Apache Spark 群集的 Azure 數據工廠管道中的活動執行。 數據流活動可以通過現有的數據工廠調度、控制、流和監視功能進行。

映射資料流可提供完全可視的體驗,無需編碼。 數據流在執行群集上運行,用於橫向擴展的數據處理。 Azure 資料工廠處理所有代碼轉換、路徑優化和數據流作業的執行。

## <a name="getting-started"></a>開始使用

要建立資料流,請在 **「工廠資源**」下選擇加號,然後選擇 **「資料流**」。。 

![新資料流程](media/data-flow/newdataflow2.png "新的資料流程")

此操作將帶您到資料流畫布,您可以在其中創建轉換邏輯。 選擇 **「添加源**」以開始配置源轉換。 有關詳細資訊,請參閱[源轉換](data-flow-source.md)。

## <a name="data-flow-canvas"></a>資料流程畫布

數據流畫布分為三個部分:頂部條形圖、圖形和配置面板。 

![畫布](media/data-flow/canvas1.png "畫布")

### <a name="graph"></a>圖形

該圖顯示轉換流。 它顯示源數據的沿線,因為它流入一個或多個接收器。 要添加新源,請選擇「**添加源**」。 要新增新轉換,請選擇現有轉換右下角的加號。

![畫布](media/data-flow/canvas2.png "畫布")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure 整合時繼資料流程屬性

![偵錯按鈕](media/data-flow/debugbutton.png "偵錯按鈕")

當您開始在 ADF 中處理數據流時,您希望打開瀏覽器 UI 頂部的資料串流的「除錯」開關。 這將啟動 Spark 群集,用於互動式調試、數據預覽和管道調試執行。 您可以通過選擇自訂[Azure 整合執行時](concepts-integration-runtime.md)來設定正在使用的群集的大小。 上次數據預覽或上次調試管道執行后,調試會話最多保持活動狀態長達 60 分鐘。

使用資料串流活動操作管道時,ADF 將使用與「運行打開」屬性中[的活動](control-flow-execute-data-flow-activity.md)關聯的 Azure 整合執行時。

預設的 Azure 整合執行時是一個小型的 4 核單輔助節點叢集,允許您以最低的成本預覽數據並快速執行調試管道。 如果要對大型資料集執行操作,則設置較大的 Azure IR 配置。

通過在 Azure IR 資料流屬性中設置 TTL,可以指示 ADF 維護群集資源 (VM) 池。 此操作可加快後續活動的作業執行速度。

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure 整合時與資料串流原則

##### <a name="execute-data-flows-in-parallel"></a>平行執行資料串流

如果在管道中並行執行數據流,ADF 將根據附加到每個活動的 Azure 整合執行時中的設置為每個活動啟動單獨的 Spark 群集。 要在 ADF 管道中設計並行執行,請添加數據流活動,而 UI 中沒有優先順序約束。

在這三個選項中,此選項可能在最短的時間內執行。 但是,每個並行數據流同時在單獨的群集上執行,因此事件的順序是非確定性的。

如果要在管道內並行執行數據流活動,建議不要使用 TTL。 此操作是因為同時使用相同的 Azure 整合執行時並行執行數據流會導致數據工廠的多個暖池實例。

##### <a name="overload-single-data-flow"></a>載入資料串流

如果將所有邏輯放在單個資料流中,ADF 將同一作業執行上下文放在單個 Spark 群集實例上。

此選項可能更具挑戰性,因為您的業務規則和業務邏輯可能會混雜在一起。 此選項也無法提供多少可重用性。

##### <a name="execute-data-flows-serially"></a>序列執行資料串流

如果在管道中串行執行數據流活動,並且在 Azure IR 配置上設置了 TTL,則 ADF 重用計算資源 (VM),從而加快後續執行時間。 您仍然會為每個執行接收新的 Spark 上下文。

在這三個選項中,此操作可能需要最長的時間才能端到端執行。 但它確實提供了每個數據流步驟中邏輯操作的完全分離。

### <a name="configuration-panel"></a>設定面板

配置面板顯示特定於當前所選轉換的設置。 如果未選擇轉換,則顯示數據流。 在總體資料流程配置中,您可以在 **「一般**」選項卡下編輯名稱和說明,或透過 **「參數」** 選項卡添加參數。有關詳細資訊,請參閱[映射資料串流參數](parameters-data-flow.md)。

每個轉換至少包含四個配置選項卡。

#### <a name="transformation-settings"></a>轉換設定

每個轉換的配置窗格中的第一個選項卡包含特定於該轉換的設置。 有關詳細資訊,請參閱該轉換的文檔頁。

![來源設定選項卡](media/data-flow/source1.png "來源設定選項卡")

#### <a name="optimize"></a>最佳化

「**優化**」選項卡包含用於配置分區方案的設置。

![優化](media/data-flow/optimize1.png "最佳化")

預設設置是 **「使用當前分區**」,它指示 Azure 數據工廠使用在 Spark 上運行的數據流本機的分區方案。 在大多數情況下,我們建議此設置。

在某些情況下,您可能需要調整分區。 例如,如果要將轉換輸出到湖中的單個檔,請在接收器轉換中選擇 **「單個分區**」。

可能想要控制分區方案的另一個案例是優化性能。 調整分區可控制數據跨計算節點的分佈和數據局部優化,這些優化會對總體數據流性能產生正和負面影響。 有關詳細資訊,請參閱[資料流性能指南](concepts-data-flow-performance.md)。

要更改任何轉換上的分割區,請選擇 **「優化**」選項卡並選擇「**設定分區**單選」 按鈕。 您會收到一系列分區選項。 最佳分區方法因數據卷、候選鍵、空值和基數而異。 

最佳做法是從預設分區開始,然後嘗試不同的分區選項。 您可以使用管道調試運行進行測試,並從監視檢視查看每個轉換分組中的執行時間和分區使用方式。 有關詳細資訊,請參閱[監察資料串](concepts-data-flow-monitoring.md)流 。

以下分區選項可用。

##### <a name="round-robin"></a>循環 

迴圈是一個簡單的分區,可在分區之間自動平均分配數據。 當您沒有良好的關鍵候選項時,請使用迴圈,以實現可靠的智慧分區策略。 您可以設定實體分割區的數目。

##### <a name="hash"></a>雜湊

Azure 資料工廠生成列哈希以生成統一的分區,以便具有相似值的行屬於同一分區。 使用哈希選項時,測試可能的分區偏斜。 您可以設定實體分割區的數目。

##### <a name="dynamic-range"></a>動態範圍

動態範圍使用基於您提供的列或表達式的 Spark 動態範圍。 您可以設定實體分割區的數目。 

##### <a name="fixed-range"></a>固定範圍

生成為分區數據列中的值提供固定範圍的運算式。 為了避免分區偏斜,在使用此選項之前,應充分了解數據。 為表達式輸入的值用作分區函數的一部分。 您可以設定實體分割區的數目。

##### <a name="key"></a>Key

如果您對資料基數有很好的瞭解,則密鑰分區可能是一個不錯的策略。 鍵分區為列中的每個唯一值創建分區。 無法設置分區數,因為該編號基於數據中的唯一值。

#### <a name="inspect"></a>檢查

群組**的**資料的資料的資料的檢視。 您可以看到列計數、列更改、添加列、數據類型、列順序和列引用。 **檢查**是元數據的唯讀檢視。 無需啟用除錯模式即可在 **「檢查」** 窗格中檢視元資料。

![檢查](media/data-flow/inspect1.png "檢查")

以轉換變更資料元件時,您將在 **「檢查」** 窗格中看到元資料更改流。 如果源轉換中沒有定義的架構,則中繼資料在 **「檢查」** 窗格中不可見。 在架構漂移方案中,缺少元數據很常見。

#### <a name="data-preview"></a>資料預覽

如果除錯模式處於開啟狀態,資料**預覽選項**卡將為您提供每個轉換時資料的互動式快照。 有關詳細資訊,請參閱[除錯模式下的資料預覽](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>頂端列

頂部欄包含影響整個數據流的操作,如保存和驗證。 您還可以使用 **「顯示圖形**」 「隱藏**圖形**」 按鈕在圖形和設定模式之間切換。

![隱藏圖形](media/data-flow/hideg.png "隱藏圖形")

如果隱藏圖形,則可以通過 **「上一步**」和 **「下一步**」按鈕橫向瀏覽轉換節點。

![前一個與下一個按鈕](media/data-flow/showhide.png "前一個與下一個按鈕")

## <a name="next-steps"></a>後續步驟

* 瞭解如何建立[來源 。](data-flow-source.md)
* 瞭解如何在[調試模式下](concepts-data-flow-debug-mode.md)構建數據流。
