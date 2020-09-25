---
title: 複製活動效能和擴充性指南
description: 瞭解當您使用複製活動時，會影響 Azure Data Factory 中資料移動效能的關鍵因素。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: a6e89883ec0e1e493bad9572876af86f4a0d3853
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324426"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>複製活動效能和擴充性指南

> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本："]
> * [第 1 版](v1/data-factory-copy-activity-performance.md)
> * [目前的版本](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

有時候您會想要從 data lake 或企業資料倉儲執行大規模資料移轉 (EDW) 到 Azure。 當您想要將大量資料從不同的來源內嵌到 Azure，以進行海量資料分析時。 在每個案例中，都是達到最佳效能和擴充性的關鍵。

Azure Data Factory (ADF) 提供內嵌資料的機制。 ADF 有下列優點：

* 處理大量資料
* 具有高效能
* 符合成本效益

這些優點讓 ADF 成為適合想要建立可高度效能之可擴充資料內嵌管線的資料工程師的絕佳選擇。

閱讀本文後，您將能夠回答下列問題：

* 使用 ADF 複製活動進行資料移轉和資料內嵌案例，可以達到何種層級的效能和擴充性？
* 我應該採取哪些步驟來調整 ADF 複製活動的效能？
* 我可以使用哪些 ADF 效能優化旋鈕來優化單一複製活動執行的效能？
* 將複製效能優化時，ADF 以外的其他哪些因素？

> [!NOTE]
> 如果您不熟悉複製活動的一般功能，請參閱「 [複製活動」總覽](copy-activity-overview.md) ，再閱讀本文。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可達到複製效能和擴充性

ADF 提供無伺服器架構，允許不同層級的平行處理原則。

此架構可讓您開發管線，將您環境的資料移動輸送量最大化。 這些管線充分利用下列資源：

* 網路頻寬
* 每秒的儲存體輸入/輸出作業 (IOPS) 和頻寬

此完整使用率表示您可以藉由測量下列資源所提供的最小輸送量，來估計整體輸送量：

* 來源資料存放區
* 目的地資料存放區
* 來源和目的地資料存放區之間的網路頻寬

下表計算複製持續時間。 持續時間取決於資料大小和您環境的頻寬限制。

&nbsp;

| 資料大小/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分鐘    | 1.4 分鐘   | 0.3 分鐘   | 0.1 分鐘  | 0.03 分鐘 | 0.01 分鐘 | 0.0 分鐘   |
| **10 GB**                   | 27.3 分鐘   | 13.7 分鐘  | 2.7 分鐘   | 1.3 分鐘  | 0.3 分鐘  | 0.1 分鐘  | 0.03 分鐘  |
| **100 GB**                  | 4.6 小時    | 2.3 小時   | 0.5 小時   | 0.2 小時  | 0.05 小時 | 0.02 小時 | 0.0 小時   |
| **1 TB**                    | 46.6 小時   | 23.3 小時  | 4.7 小時   | 2.3 小時  | 0.5 小時  | 0.2 小時  | 0.05 小時  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1 日    | 0.2 天  |
| **1 PB**                    | 64.7 月    | 32.4 月   | 6.5 月    | 3.2 月   | 0.6 月   | 0.3 月   | 0.06 月   |
| **10 PB**                   | 647.3 月   | 323.6 月  | 64.7 月   | 31.6 月  | 6.5 月   | 3.2 月   | 0.6 月    |
| | |  | | |  | | |

ADF 複製可在不同的層級進行調整：

![ADF 複製的縮放方式](media/copy-activity-performance/adf-copy-scalability.png)

* ADF 控制流程可以平行方式啟動多個複製活動，例如使用 [For Each 迴圈](control-flow-for-each-activity.md)。

* 單一複製活動可以利用可擴充的計算資源。
  * 使用 (IR) 的 Azure 整合執行時間時，您最多可以針對每個複製活動，以無伺服器方式指定 [最多256個數據整合單位 (diu) ](#data-integration-units) 。
  * 使用自我裝載 IR 時，您可以採取下列其中一種方法：
    * 手動擴大電腦。
    * 擴充到多部電腦， ([最多4個節點](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) ，而單一複製活動會在所有節點上分割其檔案集。

* 單一複製活動會以 [平行方式](#parallel-copy)使用多個執行緒讀取和寫入資料存放區。

## <a name="performance-tuning-steps"></a>效能微調步驟

採取下列步驟，以複製活動調整 Azure Data Factory 服務的效能：

1. **挑選測試資料集並建立基準。**

    在開發期間，針對代表性資料範例使用複製活動來測試您的管線。 您所選擇的資料集應該會呈現您的一般資料模式，以及下列屬性：

    * 資料夾結構
    * 檔案模式
    * 資料結構描述

    而且您的資料集應該夠大，才能評估複製效能。 良好的大小至少需要10分鐘的時間，複製活動才能完成。 在 [複製活動監視](copy-activity-monitoring.md)之後，收集執行詳細資料和效能特性。

2. **如何最大化單一複製活動的效能**：

    建議您先使用單一複製活動將效能最大化。

    * **如果正在 _Azure_ integration runtime 上執行複製活動：**

        從資料整合單位的預設值開始 [ (DIU) ](#data-integration-units) 和 [平行複製](#parallel-copy) 設定。

    * **如果在 _自我_ 裝載整合執行時間上執行複製活動：**

        建議您使用專用的電腦來裝載 IR。 電腦必須與裝載資料存放區的伺服器不同。 從 [平行複製](#parallel-copy) 設定的預設值開始，並使用自我裝載 IR 的單一節點。

    進行效能測試回合。 記下效能。 包含使用的實際值，例如 Diu 和平行複製。 請參閱 [複製活動監視](copy-activity-monitoring.md) ，以瞭解如何收集所使用的執行結果和效能設定。 瞭解如何針對 [複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md) ，以找出並解決瓶頸。

    依照疑難排解和微調指導方針進行反覆運算以進行其他效能測試回合。 一旦單一複製活動執行無法達到更高的輸送量，請考慮是否同時執行多個複本，以最大化匯總輸送量。 此選項會在下一個編號的專案符號中討論。

3. **如何藉由同時執行多個複本來將匯總輸送量最大化：**

    現在您已將單一複製活動的效能最大化。 如果您還沒有達到環境的輸送量上限，您可以平行執行多個複製活動。 您可以使用 ADF 控制流程結構，以平行方式執行。 其中一個這類結構是 [For each 迴圈](control-flow-for-each-activity.md)。 如需詳細資訊，請參閱下列有關解決方案範本的文章：

    * [從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
    * [將資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)
    * [使用控制表格進行大量複製](solution-template-bulk-copy-with-control-table.md)

4. **將設定擴展至整個資料集。**

    當您對執行結果及效能感到滿意時，您可以展開定義和管線來涵蓋整個資料集。

## <a name="troubleshoot-copy-activity-performance"></a>針對複製活動效能進行疑難排解

遵循 [效能微調步驟](#performance-tuning-steps) 來規劃和執行您案例的效能測試。 並瞭解如何針對複製 [活動效能](copy-activity-performance-troubleshooting.md)Azure Data Factory 問題進行疑難排解，以針對每個複製活動執行的效能問題進行疑難排解。

## <a name="copy-performance-optimization-features"></a>複製效能優化功能

Azure Data Factory 提供下列效能優化功能：

* [資料整合單位](#data-integration-units)
* [自我裝載整合執行時間的擴充性](#self-hosted-integration-runtime-scalability)
* [平行複製](#parallel-copy)
* [分段複製](#staged-copy)

### <a name="data-integration-units"></a>資料整合單位

資料整合單位 (DIU) 是一種量值，表示 Azure Data Factory 中單一單位的能力。 電源是 CPU、記憶體和網路資源分配的組合。 DIU 僅適用于 [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime)。 DIU 不適用於自我裝載 [的整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。 若要[深入瞭解](copy-activity-performance-features.md#data-integration-units)，請參閱。

### <a name="self-hosted-integration-runtime-scalability"></a>自我裝載整合執行時間的擴充性

您可能會想要裝載增加的並行工作負載。 或者，您可能想要在目前的工作負載層級中達到更高的效能。 您可以使用下列方法來增強處理規模：

* 您可以藉由增加可在節點上執行的[並行作業](create-self-hosted-integration-runtime.md#scale-up)數目 _，擴大自我_裝載的 IR。  
只有當節點的處理器和記憶體低於充分利用時，擴大才能運作。
* 您可以將多個節點新增 (機) ， _以擴充自我_ 裝載的 IR。

如需詳細資訊，請參閱

* [複製活動效能優化功能：自我裝載整合執行時間的擴充性](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [建立和設定自我裝載整合執行時間：調整考慮](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>平行複製

您可以設定 `parallelCopies` 屬性，以指出您希望複製活動使用的平行處理原則。 請將此屬性視為複製活動內的最大執行緒數目。 執行緒會以平行方式運作。 執行緒會從來源讀取，或寫入至接收資料存放區。 [深入了解](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>分段複製

資料複製作業可以將資料 _直接_ 傳送至接收資料存放區。 或者，您也可以選擇使用 Blob 儲存體作為 _過渡暫存_ 存放區。 [深入了解](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>後續步驟

請參閱其他複製活動文章：

* [複製活動概觀](copy-activity-overview.md)
* [針對複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md)
* [複製活動效能優化功能](copy-activity-performance-features.md)
* [使用 Azure Data Factory 將資料從 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
* [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
