---
title: 在 Azure Synapse Analytics 中新增和管理 Apache Spark 的程式庫
description: 瞭解如何在 Azure Synapse Analytics 中新增和管理 Apache Spark 所使用的程式庫。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cf78a2f7d909fb260c5ff99f80c9d1482f2cd08b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027302"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中新增和管理 Apache Spark 的程式庫

Apache Spark 取決於許多程式庫來提供功能。 這些程式庫可以擴充或取代為其他程式庫或更新版本的舊版。

您可以在 Spark 集區（預覽）層級新增 Python 套件，並可在 Spark 作業定義層級新增以 .jar 為基礎的套件。

## <a name="adding-or-updating-python-libraries"></a>新增或更新 Python 程式庫

Azure Synapse 分析中的 Apache Spark 有完整的 Anacondas 安裝加上額外的程式庫。 您可以在[Apache Spark 版本支援](apache-spark-version-support.md)中找到完整的程式庫清單。

當 Spark 實例啟動時，會使用此安裝作為基底來建立新的虛擬環境。 此外，也可以使用*requirements.txt*檔案（命令的輸出 `pip freeze` ）來升級虛擬環境。 此檔案中所列用於安裝或升級的封裝，會在叢集啟動時從 PyPi 下載。 每次從該 Spark 集區建立 Spark 實例時，都會使用此需求檔案。

> [!IMPORTANT]
>
> - 如果您要安裝的套件很大或需要很長的時間才能安裝，這會影響 Spark 實例的啟動時間。
> - 不支援在安裝時需要編譯器支援的套件，例如 GCC。
> - 無法降級封裝，只可進行新增或升級。

### <a name="requirements-format"></a>需求格式

下列程式碼片段顯示需求檔案的格式。 PyPi 套件名稱會連同確切的版本一起列出。 這個檔案會遵循[pip 凍結](https://pip.pypa.io/en/stable/reference/pip_freeze/)參考檔中所述的格式。 這個範例會釘選特定版本。 您也可以在此檔案中指定「不大於」和「小於」版本。

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python 程式庫使用者介面

新增程式庫的 UI 位於 Azure 入口網站上 [**建立 Apache Spark 集**區] 頁面的 [**其他設定**] 索引標籤中。

在頁面的 [**套件**] 區段中，使用檔案選取器上傳環境設定檔。

![新增 Python 程式庫](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "新增 Python 程式庫")

### <a name="verifying-installed-libraries"></a>驗證已安裝的程式庫

若要確認是否已安裝正確的正確程式庫版本，請執行下列程式碼

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文件](https://spark.apache.org/docs/2.4.4/)
