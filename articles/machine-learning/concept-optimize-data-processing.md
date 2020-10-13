---
title: 最佳化資料處理
titleSuffix: Azure Machine Learning
description: 瞭解優化資料處理速度的最佳作法，以及 Azure Machine Learning 支援大規模資料處理的整合。
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: c73a5c5339403ecd91d45968405682c59f2f23b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719269"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>使用 Azure Machine Learning 優化資料處理

在本文中，您將瞭解最佳作法，以協助您在本機和大規模優化資料處理速度。

Azure Machine Learning 已與開放原始碼套件和架構整合，以進行資料處理。 藉由使用這些整合並套用本文中的最佳作法建議，您可以在本機和大規模改善資料處理速度。

## <a name="parquet-and-csv-file-formats"></a>Parquet 和 CSV 檔案格式

以逗號分隔的值 (csv) 檔案是資料處理的一般檔案格式。 不過，建議使用 parquet 檔案格式進行機器學習工作。

[Parquet](https://parquet.apache.org/) 檔案會以二進位單欄式格式儲存資料。 如果需要將資料分割成多個檔案，此格式會很有用。 此外，此格式可讓您以機器學習服務實驗的相關欄位為目標。 您可以藉由選取用來定型 ML 模型的必要資料行，來減少資料載入，而不需要讀取 20 GB 的資料檔案。 Parquet 檔案也可以壓縮來將處理能力降至最低，並佔用較少的空間。

CSV 檔案通常用來匯入和匯出資料，因為它們很容易在 Excel 中編輯和讀取。 Csv 中的資料會以資料列格式儲存為字串，而且可以壓縮檔案來減少資料傳輸負載。 未壓縮的 Csv 可依大約2-10 的因素擴充，而壓縮的 Csv 可以更進一步增加。 如此一來，記憶體中的 5 GB CSV 就會擴充到您電腦上的 8 GB RAM。 這種壓縮行為可能會增加資料傳輸延遲，如果您有大量的資料要處理，這並不理想。 

## <a name="pandas-dataframe"></a>Pandas 資料框架

[Pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) 通常用於資料操作和分析。 `Pandas` 適用于小於 1 GB 的資料大小，但 `pandas` 在檔案大小達到約 1 gb 的情況下，資料框架的處理時間變慢。 這種緩慢的原因是儲存體中的資料大小與資料框架中的資料大小不同。 例如，CSV 檔案中的資料最多可在資料框架中擴大至10倍，因此 1 GB 的 CSV 檔案在資料框架中可能會變成 10 GB。

`Pandas` 是單一執行緒，表示作業會一次在單一 CPU 上執行一次。 您可以輕鬆地將工作負載平行處理至單一 Azure Machine Learning 計算實例上的多個[Modin](https://modin.readthedocs.io/en/latest/)虛擬 cpu，此實例具有 `Pandas` 使用分散式後端包裝的 Modin 套件。

若要使用和 Dask 來平行處理您的工作 `Modin` ，只需將這行程式碼變更[Dask](https://dask.org) `import pandas as pd` 為 `import modin.pandas as pd` 。

## <a name="dataframe-out-of-memory-error"></a>資料框架：記憶體不足錯誤 

當您的資料框架擴充到電腦上的可用 RAM 時，通常會發生 *記憶體不足的* 錯誤。 這個概念也適用于或之類的分散式 `Modin` 架構 `Dask` 。  也就是說，您的作業會嘗試將資料框架載入叢集中每個節點的記憶體中，但沒有足夠的 RAM 可供使用。

其中一個解決方案是增加您的 RAM 以容納記憶體中的資料框架。 我們建議您的計算大小和處理能力包含 RAM 大小的兩倍。 因此，如果您的資料框架為 10 GB，請使用至少具有 20 GB RAM 的計算目標，以確保資料框架可以輕鬆地放入記憶體中並進行處理。 

針對多個虛擬 Cpu （vCPU），請記住，您希望一個磁碟分割可以輕鬆地放入每個 vCPU 在電腦上所能擁有的 RAM。 也就是說，如果您有 16 GB 的 RAM 4 個 vcpu，您需要每個 vCPU 大約有 2 GB 的資料框架。

### <a name="local-vs-remote"></a>本機與遠端

您可能會注意到，當您在本機電腦上工作時，某些 pandas 資料框架命令的執行速度會更快，而您也可以使用 Azure Machine Learning 來布建 您的本機電腦通常會啟用分頁檔，讓您可以載入超過實體記憶體的空間，也就是您的硬碟用來作為 RAM 的擴充功能。 目前，Azure Machine Learning Vm 沒有分頁檔執行，因此只能載入可用實體 RAM 的資料量。 

針對計算繁重的作業，建議您挑選較大的 VM 來改善處理速度。

深入瞭解可用的 [VM 系列和](concept-compute-target.md#supported-vm-series-and-sizes) Azure Machine Learning 的大小。 

如需 RAM 規格，請參閱對應的 VM 系列頁面，例如 [Dv2-Dsv2 系列](../virtual-machines/dv2-dsv2-series-memory.md) 或 [NC 系列](../virtual-machines/nc-series.md)。

### <a name="minimize-cpu-workloads"></a>最小化 CPU 工作負載

如果您無法將更多 RAM 新增至您的電腦，您可以套用下列技術，以協助將 CPU 工作負載降到最低，並將處理時間優化。 這些建議適用于單一和分散式系統。

技巧 | 描述
----|----
壓縮 | 使用不同的資料標記法，方法是使用較少的記憶體，而且不會大幅影響您的計算結果。<br><br>*範例：* 您可以將專案儲存為布林值、True 或 False （可儲存為1個位元組），而不會將專案儲存為每個專案大約10個位元組或更多的字串。
區塊化 | 將資料載入至記憶體中的子集 (區塊) 、一次處理一個子集的資料，或平行處理多個子集。 如果您需要處理所有資料，但不需要一次將所有資料載入到記憶體中，此方法的效果最好。 <br><br>*範例：* 一次只處理一年的資料，而不是一次處理一整天的資料，而是一次載入和處理資料。
編製索引 | 套用並使用索引，此摘要會告訴您要在哪裡找到您所關心的資料。 當您只需要使用資料的子集，而不是完整的資料集時，索引編制會很有用。<br><br>*範例：* 如果您有一年的銷售資料依月份排序，索引可協助您快速搜尋想要處理的月份。

## <a name="scale-data-processing"></a>調整資料處理

如果先前的建議不足，而且您無法取得適合您資料的虛擬機器，則可以 

* 使用或之類的 `Spark` 架構 `Dask` 來處理「記憶體不足」的資料。 在此選項中，資料框架會依分割區載入至 RAM 分割區並進行處理，並在結尾收集最終結果。  

* 使用分散式架構來擴充至叢集。 在此選項中，資料處理負載會在多個平行運作的 Cpu 上進行分割和處理，並在結尾收集最終結果。

### <a name="recommended-distributed-frameworks"></a>建議的分散式架構

下表根據您的程式碼喜好設定或資料大小，建議與 Azure Machine Learning 整合的分散式架構。

體驗或資料大小 | 建議
------|------
如果您很熟悉 `Pandas`| `Modin` 或 `Dask` 資料框架
如果您想要 `Spark` | `PySpark`
適用于小於 1 GB 的資料 | `Pandas` 本機 **或** 遠端 Azure Machine Learning 計算實例
大於 10 GB 的資料| 使用 `Ray` 、或，移至 `Dask` 叢集 `Spark`

您可以 `Dask` 使用 [dask-cloudprovider 使用](https://cloudprovider.dask.org/en/latest/#azure) 套件在 Azure ML compute 叢集上建立叢集。 或者，您可以 `Dask` 在計算實例的本機上執行。

## <a name="next-steps"></a>接下來的步驟

* [Azure Machine Learning 的資料內嵌選項](concept-data-ingestion.md)。
* [建立和註冊資料集](how-to-create-register-datasets.md)。
