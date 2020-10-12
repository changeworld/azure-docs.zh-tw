---
title: 在 SQL Server 虛擬機器中瀏覽資料 - Team Data Science Process
description: 如何使用 SQL 或類似 Python 的程式設計語言，在 Azure 上瀏覽儲存在 SQL Server VM 中的資料。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085645"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>在 Azure 上瀏覽 SQL Server 虛擬機器中的資料

本文涵蓋如何瀏覽儲存在 Azure 上 SQL Server VM 中的資料。 使用 SQL 或 Python 來檢查資料。

此工作是 [Team Data Science Process](overview.md) 中的一個步驟。

> [!NOTE]
> 本文件中的 SQL 陳述式範例假設資料位於 SQL Server 中。 如果不是，請參閱雲端資料科學程序圖，以了解如何將資料移至 SQL Server。
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>使用 SQL 指令碼瀏覽 SQL 資料
以下是數個 SQL 指令碼範例，可用來探索儲存於 SQL Server 中的資料。

1. 取得每天的觀察計數
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. 取得類別資料行中的層級 
   
    `select  distinct <column_name> from <databasename>`
3. 取得兩個類別資料行組合中的層級數目 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. 取得數值資料行的分佈
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> 如需實用範例，您可以使用 [NYC 計程車資料集](https://www.andresmh.com/nyctaxitrips/)，並參考標題為[使用 IPython Notebook 和 SQL Server 來處理有爭議的 NYC 資料](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)的 IPNB，以進行端對端逐步解說。
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>使用 Python 瀏覽 SQL 資料
當資料位於 SQL Server 時，使用 Python 來流覽資料並產生功能，類似于使用 Python 在 Azure blob 中處理資料，如在 [資料科學環境中處理 Azure blob 資料](data-blob.md)所述。 將資料庫中的資料載入至 pandas 資料框架，然後再進一步處理。 我們將在本節中說明連接到資料庫以及將資料載入資料框架的程序。

下列連接字串格式可用來使用 pyodbc (使用您的特定值來取代 servername、dbname、username 和 password)，從 Python 連接到 SQL Server 資料庫：

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Python 中的 [Pandas 程式庫](https://pandas.pydata.org/) 提供一組豐富的資料結構和資料分析工具，可用於 python 程式設計的資料操作。 下列程式碼會將從 SQL Server 資料庫傳回的結果讀取至 Pandas 資料框架：

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

現在您可以利用 [在資料科學環境中處理 Azure Blob 資料](data-blob.md)主題中說明的方式來使用 Pandas 資料框架。

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process 實務範例
如需使用公用資料集進行 Cortana Analytics 程序的端對端逐步解說範例，請參閱 [Team Data Science Process 實務：使用 SQL Server](sql-walkthrough.md)。

