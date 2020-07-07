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
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414165"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>複製活動效能和擴充性指南

> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本："]
> * [第 1 版](v1/data-factory-copy-activity-performance.md)
> * [目前的版本](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

無論您想要執行從 data lake 或企業資料倉儲（EDW）到 Azure 的大規模資料移轉，或想要將大規模資料從不同來源內嵌到 Azure 以進行 big data 分析，都必須達到最佳效能和擴充性。  Azure Data Factory 提供高效能、彈性且符合成本效益的機制來內嵌資料，讓資料工程師更適合想要建立高效能且可調整規模的資料內嵌管線。

閱讀本文後，您將能夠回答下列問題：

- 我可以針對資料移轉和資料內嵌案例使用 ADF 複製活動，達到何種層級的效能和擴充性？

- 為了微調 ADF 複製活動的效能，我應該採取哪些步驟？
- 我可以使用哪些 ADF 效能優化旋鈕，將單一複製活動執行的效能優化？
- 在優化複製效能時，ADF 以外的其他因素會被考慮嗎？

> [!NOTE]
> 如果您一般不熟悉複製活動，請參閱[複製活動總覽](copy-activity-overview.md)，再閱讀本文。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可達到複製效能和擴充性

ADF 提供無伺服器的架構，其可供以不同層級進行平行處理，允許開發人員建置管線來充分利用網路頻寬以及儲存體 IOPS 和頻寬來最大化環境的資料移動輸送量。  這表示您可以藉由測量來來源資料存放區、目的地資料存放區，以及來源與目的地之間的網路頻寬所提供的最小輸送量來估計您可達到的輸送量。  下表根據您環境的資料大小和頻寬限制來計算複製持續時間。 

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

ADF 複製可在不同層級進行調整：

![ADF 複製的縮放方式](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 控制流程可以平行方式啟動多個複製活動，例如使用 [For Each 迴圈](control-flow-for-each-activity.md)。
- 單一複製活動可利用可調整的計算資源：使用 Azure Integration Runtime 時，您可以無伺服器的方式為每個複製活動指定[最多 256 個 DIU](#data-integration-units)；使用自我裝載整合執行階段時，您可手動擴大機器或擴增至多部機器 ([最多 4 個節點](create-self-hosted-integration-runtime.md#high-availability-and-scalability))，且單一複製活動會將其檔案集分割至所有節點。
- 單一複製活動會[以平行方式](#parallel-copy)使用多個執行緒讀取和寫入資料存放區。

## <a name="performance-tuning-steps"></a>效能微調步驟

採取下列步驟，使用複製活動來微調 Azure Data Factory 服務的效能。

1. **挑選測試資料集並建立基準。** 在開發階段，使用複製活動針對代表性資料範例來測試您的管線。 您選擇的資料集應該代表您的一般資料模式（資料夾結構、檔案模式、資料結構描述等等），而且夠大以評估複製的效能，例如，需要10分鐘或更久的時間才能完成複製活動。 在[複製活動監視](copy-activity-monitoring.md)之後收集執行詳細資料和效能特性。

2. **如何將單一複製活動的效能最大化**：

   一開始，我們建議您先使用單一複製活動來最大化效能。

   - **如果複製活動是在 Azure Integration Runtime 上執行：** 開始使用[資料整合單位（DIU）](#data-integration-units)的預設值和[平行複製](#parallel-copy)設定。 

   - **如果要在自我裝載的 Integration Runtime 上執行複製活動：** 我們建議您將專用的電腦與裝載資料存放區的伺服器分開使用，以裝載整合執行時間。 從[平行複製](#parallel-copy)設定的預設值開始，並使用自我裝載 IR 的單一節點。  

   執行效能測試回合，並記下所達到的效能，以及實際使用的值，例如 Diu 和平行複製。 請參閱[複製活動監視](copy-activity-monitoring.md)以瞭解如何收集所使用的執行結果和效能設定，並瞭解如何[疑難排解複製活動的效能](copy-activity-performance-troubleshooting.md)，以找出並解決瓶頸。 

   依照疑難排解和微調指導方針，逐一查看以進行其他效能測試回合。 一旦單一複製活動執行無法達到更佳的輸送量，請考慮同時執行多個複本，同時參考步驟3，以最大化匯總輸送量。


3. **如何藉由同時執行多個複本，將匯總輸送量最大化：**

   現在您已將單一複製活動的效能最大化，如果您尚未達到環境的輸送量上限（網路、來源資料存放區和目的地資料存放區），您可以使用 ADF 控制流程結構（例如[For each 迴圈](control-flow-for-each-activity.md)）平行執行多個複製活動。 請參閱[從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案、將[資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)，或[使用控制資料表](solution-template-bulk-copy-with-control-table.md)解決方案範本作為一般範例進行大量複製。

5. **將設定擴充到整個資料集。** 當您對執行結果及效能感到滿意時，可以展開定義和管線來涵蓋整個資料集。

## <a name="troubleshoot-copy-activity-performance"></a>針對複製活動效能進行疑難排解

請遵循[效能微調步驟](#performance-tuning-steps)，為您的案例規劃和執行效能測試。 並瞭解如何針對[複製活動效能疑難排解](copy-activity-performance-troubleshooting.md)Azure Data Factory 中的每個複製活動執行效能問題進行疑難排解。

## <a name="copy-performance-optimization-features"></a>複製效能優化功能

Azure Data Factory 提供下列效能優化功能：

- [資料整合單位](#data-integration-units)
- [自我裝載整合執行時間的擴充性](#self-hosted-integration-runtime-scalability)
- [平行複製](#parallel-copy)
- [分段複製](#staged-copy)

### <a name="data-integration-units"></a>資料整合單位

資料整合單位是一種量值，代表 Azure Data Factory 中單一單位的能力（CPU、記憶體和網路資源分配的組合）。 資料整合單位僅適用于[Azure 整合運行](concepts-integration-runtime.md#azure-integration-runtime)時間，但不會套用至[自我裝載整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。 [深入了解](copy-activity-performance-features.md#data-integration-units)。

### <a name="self-hosted-integration-runtime-scalability"></a>自我裝載整合執行時間的擴充性

若要裝載增加的並行工作負載，或要達到更高的效能，您可以擴充或相應放大自我裝載的 Integration Runtime。 [深入了解](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)。

### <a name="parallel-copy"></a>平行複製

您可以設定 [平行複製]，以指出您想要複製活動使用的平行處理原則。 您可以將此屬性視為複製活動內的最大執行緒數目，這會從來源讀取或以平行方式寫入接收資料存放區。 [深入了解](copy-activity-performance-features.md#parallel-copy)。

### <a name="staged-copy"></a>分段複製

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 [深入了解](copy-activity-performance-features.md#staged-copy)。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [針對複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md)
- [複製活動效能優化功能](copy-activity-performance-features.md)
- [使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
