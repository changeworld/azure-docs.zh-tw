---
title: 最佳化資料處理
titleSuffix: Azure Machine Learning
description: 瞭解優化資料處理速度的最佳做法，以及 Azure Machine Learning 支援大規模資料處理的整合。
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 6bb85ada5ab1cd443d47ed85024b45d98354e97f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500958"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>使用 Azure Machine Learning 優化資料處理

在本文中，您將瞭解可協助您在本機和大規模優化資料處理速度的最佳做法。

Azure Machine Learning 已與用於資料處理的開放原始碼套件和架構整合。 藉由使用這些整合並套用本文中的最佳作法建議，您可以在本機和大規模地改善資料處理速度。

## <a name="parquet-and-csv-file-formats"></a>Parquet 和 CSV 檔案格式

逗號分隔值（csv）檔案是資料處理的一般檔案格式。 不過，建議您針對機器學習工作使用 parquet 檔案格式。

[Parquet files](https://parquet.apache.org/)會以二進位單欄式格式儲存資料。 如果需要將資料分割成多個檔案，此格式會很有用。 此外，這種格式可讓您以機器學習實驗的相關欄位作為目標。 您可以藉由選取必要的資料行來定型 ML 模型，而不需要讀取 20 GB 的資料檔案。 Parquet 檔案也可以進行壓縮，將處理能力降到最低，並佔用較少的空間。

CSV 檔案通常用來匯入和匯出資料，因為在 Excel 中很容易就能編輯和讀取。 Csv 中的資料會以資料列格式儲存為字串，而且可以壓縮檔案以減少資料傳輸負載。 未壓縮的 Csv 可以擴充約2-10 的因素，而壓縮的 Csv 也可以進一步增加。 因此，記憶體中的 5 GB CSV 會擴展到您電腦上的 8 GB RAM。 這種壓縮行為可能會增加資料傳輸延遲，如果您有大量資料要處理，這不是理想的選擇。 

## <a name="pandas-dataframe"></a>Pandas 資料框架

[Pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)通常用於資料操作和分析。 `Pandas`適用于小於 1 GB 的資料大小，但 `pandas` 當檔案大小達到 1 gb 時，資料框架的處理時間會變慢。 這種速度會變慢，因為儲存體中的資料大小與資料框架中的資料大小不同。 比方說，CSV 檔案中的資料最多可以在資料框架中展開10次，因此 1 GB 的 CSV 檔案在資料框架中可能會變成 10 GB。

`Pandas`是單一執行緒，表示作業會在單一 CPU 上一次執行一個。 您可以輕鬆地將工作負載平行處理至單一 Azure Machine Learning 計算實例上的多個[Modin](https://modin.readthedocs.io/en/latest/)虛擬 cpu，其具有 `Pandas` 使用分散式後端包裝的套件，例如 Modin。

若要使用和 Dask 將您的工作平行化 `Modin` ，只要將這行程式碼變更[Dask](https://dask.org) `import pandas as pd` 為即可 `import modin.pandas as pd` 。

## <a name="dataframe-out-of-memory-error"></a>資料框架：記憶體不足錯誤 

當您的資料框架擴充到您電腦上的可用 RAM 上方時，通常會發生*記憶體不足*的錯誤。 這個概念也適用于分散式架構，例如 `Modin` 或 `Dask` 。  也就是說，您的作業會嘗試在叢集中每個節點的記憶體中載入資料框架，但沒有足夠的 RAM 可用來執行這項操作。

其中一個解決方案是增加您的 RAM，以符合記憶體中的資料框架。 我們建議您的計算大小和處理能力包含 RAM 大小的兩倍。 因此，如果您的資料框架是 10 GB，請使用至少具有 20 GB RAM 的計算目標，以確保資料框架可輕鬆地納入記憶體中並加以處理。 

針對多個虛擬 Cpu （vCPU），請記住，您想要讓一個資料分割適當地符合每個 vCPU 在機器上可以擁有的 RAM。 也就是說，如果您有 16 GB RAM 4 個 vcpu，您會想要每個 vCPU 大約有 2 GB 的資料框架。

### <a name="minimize-cpu-workloads"></a>最小化 CPU 工作負載

如果您無法將更多 RAM 新增至您的電腦，您可以套用下列技術，以協助將 CPU 工作負載最小化，並將處理時間優化。 這些建議同時適用于單一和分散式系統。

技巧 | 說明
----|----
壓縮 | 針對您的資料使用不同的標記法，方法是使用較少的記憶體，而不會大幅影響您的計算結果。<br><br>*範例：* 不是將專案儲存為每個專案大約10個位元組以上的字串，而是將其儲存為布林值 True 或 False，您可以將其儲存在1個位元組中。
區塊化 | 將資料載入子集（區塊）中的記憶體，一次處理一個子集的資料，或以平行方式處理多個子集。 如果您需要處理所有資料，但不需要一次將所有資料載入記憶體中，則這個方法最適合。 <br><br>*範例：* 不需要一次處理一整天的資料，而是一次載入及處理一個月的資料。
編製索引 | 套用並使用索引，這是一個摘要，告訴您哪裡可以找到您關心的資料。 當您只需要使用資料的子集，而不是完整的集合時，索引編制就很有用。<br><br>*範例：* 如果您有多年來依月份排序的銷售資料，索引可協助您快速搜尋想要處理的月份。

## <a name="scale-data-processing"></a>調整資料處理

如果先前的建議不夠，而且您無法取得適合您資料的虛擬機器，您可以 

* 使用或之類的 `Spark` 架構 `Dask` 來處理資料「記憶體不足」。 在此選項中，資料框架會依分割區載入 RAM 分割區並加以處理，最後的結果會在結尾收集。  

* 使用分散式架構向外擴充至叢集。 在此選項中，資料處理負載會在多個平行處理的 Cpu 上分割和處理，最後在結尾收集最終結果。

### <a name="recommended-distributed-frameworks"></a>建議的分散式架構

下表建議根據您的程式碼喜好設定或資料大小，與 Azure Machine Learning 整合的分散式架構。

經驗或資料大小 | 建議
------|------
如果您很熟悉`Pandas`| `Modin`或 `Dask` 資料框架
如果您想要`Spark` | `PySpark`
適用于小於 1 GB 的資料 | `Pandas`本機**或**遠端 Azure Machine Learning 計算實例
大於 10 GB 的資料| 使用 `Ray` 、或移至叢集 `Dask``Spark`

您可以 `Dask` 使用[dask-cloudprovider 使用](https://cloudprovider.dask.org/en/latest/#azure)套件在 Azure ML 計算叢集上建立叢集。 或者，您可以 `Dask` 在計算實例本機上執行。

## <a name="next-steps"></a>後續步驟

* [具有 Azure Machine Learning 的資料內嵌選項](concept-data-ingestion.md)。
* [建立並註冊資料集](how-to-create-register-datasets.md)。
