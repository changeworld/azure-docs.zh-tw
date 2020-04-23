---
title: 在 Azure 突觸分析中加入及管理 Apache Spark 的庫
description: 瞭解如何在 Azure 同步分析中添加和管理 Apache Spark 使用的庫。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870371"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure 突觸分析中加入及管理 Apache Spark 的庫

Apache Spark依賴於許多庫來提供功能。 這些庫可以增強或替換為其他庫或舊庫的更新版本。

可以在 Spark 池(預覽)級別添加 Python 包,也可以在 Spark 作業定義級別添加基於 .jar 的包。

## <a name="adding-or-updating-python-libraries"></a>新增或更新 Python 函式庫

Azure Synapse 分析中的 Apache Spark 具有完整的 Anacondas 安裝以及其他庫。 完整的庫清單可以在[Apache Spark 版本支援](apache-spark-version-support.md)中找到。

當 Spark 實例啟動時,將使用此安裝作為基礎創建新的虛擬環境。 此外,*要求.txt*檔(來自`pip freeze`命令的 輸出)可用於升級虛擬環境。 此檔中列出的用於安裝或升級的套件在群集啟動時從 PyPi 下載。 每次從 Spark 池創建 Spark 實例時,都會使用此要求檔。

> [!IMPORTANT]
>
> - 如果您要安裝的包很大或安裝時間過長,這會影響 Spark 實例的啟動時間。
> - 不支援在安裝時需要編譯器支援的包,如 GCC。
> - 無法降級、僅添加或升級包。

### <a name="requirements-format"></a>需求格式

以下代碼段顯示需求檔的格式。 PyPi 包名稱隨確切版本一起列出。 此檔遵循[點凍結](https://pip.pypa.io/en/stable/reference/pip_freeze/)參考文檔中所述的格式。 本示例固定特定版本。 您還可以在此檔中指定"不大於"和"小於"版本。

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python 函式庫使用者介面

用於新增函式庫的 UI 位於 Azure 門戶上的「**建立 Apache Spark 池」** 頁上的「**其他設定」** 選項卡中。

使用頁面「**包」** 部分中的檔案選擇器上載環境設定檔。

![新增 Python 函式庫](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "新增 Python 函式庫")

### <a name="verifying-installed-libraries"></a>驗證已安裝的庫

要驗證是否安裝了正確函式庫的正確版本,請執行以下代碼

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文件](https://spark.apache.org/docs/2.4.4/)
