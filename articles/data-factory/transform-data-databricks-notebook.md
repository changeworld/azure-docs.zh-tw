---
title: 使用 Databricks 筆記本轉換資料
description: 了解如何藉由執行 Databricks Notebook 來處理或轉換資料。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81419012"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>執行 Databricks Notebook 來轉換資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Data Factory 管線](concepts-pipelines-activities.md)中的 Azure Databricks 筆記本活動會在您的 Azure Databricks 工作區中執行 Databricks 筆記本。 本文是以 [資料轉換活動](transform-data.md)   一文為基礎，其中提供資料轉換和支援的轉換活動的一般總覽。Azure Databricks 是用於執行 Apache Spark 的受控平台。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook 活動定義

以下是 Databricks Notebook 活動的 JSON 定義範例：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook 活動屬性

下表說明 JSON 定義中使用的 JSON 屬性：

|屬性|描述|必要|
|---|---|---|
|NAME|管線中的活動名稱。|是|
|description|說明活動用途的文字。|否|
|type|若是 Databricks Notebook 活動，則活動類型是 DatabricksNotebook。|是|
|linkedServiceName|Databricks Notebook 執行所在之 Databricks 連結服務的名稱。 若要深入瞭解此連結服務，請參閱 [計算連結服務](compute-linked-services.md)一   文。|是|
|notebookPath|要在 Databricks 工作區中執行之 Notebook 的絕對路徑。 此路徑必須以斜線開頭。|是|
|baseParameters|機碼值組的陣列。 基礎映像參數可以用於每個活動執行。 如果 Notebook 採用未指定的參數，則系統會使用 Notebook 的預設值。 在 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair) 中尋找更多參數的相關資料。|否|
|程式庫|要在負責執行工作的叢集上，即將安裝的程式庫清單。 它可以是的陣列 \<string, object> 。|否|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks 活動支援的程式庫

在上述的 Databricks 活動定義中，您可以指定下列程式庫類型： *jar*、*蛋狀物*、 *.whl*、 *maven*、 *pypi*、 *cran*。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

如需詳細資訊，請參閱 [Databricks 文件](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)，了解程式庫類型。

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>在筆記本與 Data Factory 之間傳遞參數

您可以使用 databricks 活動中的*baseParameters*屬性，將 data factory 參數傳遞至筆記本。 

在某些情況下，您可能需要將某些值從筆記本傳回 data factory，以用於 data factory 中的控制流程（條件式檢查），或由下游活動取用（大小限制為 2 MB）。 

1. 在您的筆記本中，您可以呼叫[dbutils.fs （"returnValue"）](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) ，並將對應的 "returnValue" 傳回到 data factory。

2. 您可以使用運算式（例如）來取用 data factory 中的輸出 `'@activity('databricks notebook activity name').output.runOutput'` 。 

   > [!IMPORTANT]
   > 如果您要傳遞 JSON 物件，您可以藉由附加屬性名稱來取得值。 範例：`'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>如何在 Databricks 中上傳程式庫

#### <a name="using-databricks-workspace-ui"></a>[使用 Databricks 工作區 UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library) \(英文\)

若要取得利用 UI 新增之程式庫的 dbfs 路徑，您可以使用 [Databricks CLI (安裝)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) \(英文\)。 

使用 UI 時，Jar 程式庫通常會儲存在 dbfs: FileStore/jar。 您可以透過 CLI 來列出所有 Jar 程式庫：databricks fs ls dbfs:/FileStore/jars**。



#### <a name="copy-library-using-databricks-cli"></a>[使用 Databricks CLI 來複製程式庫](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs) \(英文\)

範例：*databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
