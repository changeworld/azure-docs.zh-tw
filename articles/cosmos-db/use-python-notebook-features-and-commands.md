---
title: 使用 Azure Cosmos DB Python 筆記本 (預覽) 中的內建筆記本命令和功能
description: 了解如何透過 Azure Cosmos DB 的內建 Python 筆記本，使用內建的命令和功能來執行一般作業。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: a16b95249562db98d9382f8ca56bf4a27beba2fd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743499"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>使用 Azure Cosmos DB Python 筆記本 (預覽) 中的內建筆記本命令和功能

Azure Cosmos DB 中的內建 Jupyter 筆記本可供從 Azure 入口網站分析資料並將其視覺化。 本文描述如何使用 Python 筆記本中的內建筆記本命令和功能來執行一般作業。

## <a name="install-a-new-package"></a>安裝新的套件
為 Azure Cosmos 帳戶啟用筆記本支援之後，即可開啟新的筆記本並安裝套件。

在新的程式碼儲存格中，插入並執行下列程式碼，並將 ``PackageToBeInstalled`` 取代為所需的 Python 套件。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
此套件將可從 Azure Cosmos 帳戶工作區中的任何筆記本使用。 

> [!TIP]
> 如果筆記本需要自訂套件，則建議在筆記本中新增儲存格以安裝套件，因為如果[重設工作區](#reset-notebooks-workspace)，則會移除套件。  

## <a name="run-a-sql-query"></a>執行 SQL 查詢

您可使用 ``%%sql`` magic 命令，以對帳戶中的任何容器執行 [SQL 查詢](sql-query-getting-started.md)。 使用語法：

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- 將 ``{database_id}`` 和 ``{container_id}`` 取代為 Cosmos 帳戶中的資料庫和容器名稱。 如果未提供 ``--database`` 和 ``--container`` 引數，則會在[預設資料庫和容器](#set-default-database-for-queries)上執行查詢。
- 您可執行 Azure Cosmos DB 中有效的任何 SQL 查詢。 查詢文字必須置於新行。

例如： 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
在儲存格中執行 ```%%sql?```，以查看筆記本中 SQL magic 命令的說明文件。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>執行 SQL 查詢並輸出到 Pandas 資料框架

您可將 ``%%sql`` 查詢的結果輸出到 [Pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)中。 使用語法： 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- 將 ``{database_id}`` 和 ``{container_id}`` 取代為 Cosmos 帳戶中的資料庫和容器名稱。 如果未提供 ``--database`` 和 ``--container`` 引數，則會在[預設資料庫和容器](#set-default-database-for-queries)上執行查詢。
- 將 ``{outputDataFrameVar}`` 取代為包含結果的資料框架變數名稱。
- 您可執行 Azure Cosmos DB 中有效的任何 SQL 查詢。 查詢文字必須置於新行。 

例如：

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>設定查詢的預設資料庫
您可設定 ```%%sql``` 命令將用於筆記本的預設資料庫。 將 ```{database_id}``` 取代為您的資料庫名稱。

```python
%database {database_id}
```
在儲存格中執行 ```%database?```，以查看筆記本中的文件。

## <a name="set-default-container-for-queries"></a>設定查詢的預設容器
您可設定 ```%%sql``` 命令將用於筆記本的預設容器。 將 ```{container_id}``` 取代為容器名稱。

```python
%container {container_id}
```
在儲存格中執行 ```%container?```，以查看筆記本中的文件。

## <a name="upload-json-items-to-a-container"></a>將 JSON 項目上傳至容器
您可使用 ``%%upload`` magic 命令，將 JSON 檔案中資料上傳至指定的 Azure Cosmos 容器。 請使用下列命令來上傳項目：

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- 將 ``{database_id}`` 和 ``{container_id}`` 取代為 Azure Cosmos 帳戶中的資料庫和容器名稱。 如果未提供 ``--database`` 和 ``--container`` 引數，則會在[預設資料庫和容器](#set-default-database-for-queries)上執行查詢。
- 將 ``{url_location_of_file}`` 取代為 JSON 檔案的位置。 該檔案必須是有效 JSON 物件的陣列，且應該可透過公用網際網路存取。

例如：

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
透過輸出統計資料，您可計算用來上傳項目的有效 RU/秒。 例如，如果在 38 秒內使用了 25,000 個 RU，則有效 RU/秒就是 25,000 個 RU / 38 秒 = 658 RU/秒。

## <a name="run-another-notebook-in-current-notebook"></a>在目前的筆記本中執行另一個筆記本 
您可使用 ``%%run`` magic 命令，從工作區中目前的筆記本執行另一個筆記本。 使用語法：

```python
%%run ./path/to/{notebookName}.ipynb
```
將 ``{notebookName}`` 取代為想要執行的筆記本名稱。 筆記本必須位於目前的「我的筆記本」工作區中。 

## <a name="use-built-in-nteract-data-explorer"></a>使用內建的 nteract 資料總管
您可使用內建的 [nteract 資料總管](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)來篩資料框架並將其視覺化。 若要啟用這項功能，請將選項 ``pd.options.display.html.table_schema`` 設定為 ``True``，並將 ``pd.options.display.max_rows`` 設定為所需的值 (您可將 ``pd.options.display.max_rows`` 設定為 ``None`` 以顯示所有結果)。

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract 資料總管](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>使用內建的 Python SDK
第 4 版的[適用於 SQL API 的 Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 會在安裝後包含在 Azure Cosmos 帳戶的筆記本環境中。

使用內建的 ``cosmos_client`` 執行個體來執行任何 SDK 作業。 

例如：

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
請參閱 [Python SDK 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples) (英文)。 

> [!IMPORTANT]
> 只有 SQL (Core) API 帳戶支援內建的 Python SDK。 針對其他 API，您必須[安裝對應於 API 的相關 Python 驅動程式](#install-a-new-package)。 

## <a name="create-a-custom-instance-of-cosmos_client"></a>建立 ``cosmos_client`` 的自訂執行個體
如需更多彈性，您可建立 ``cosmos_client`` 的自訂執行個體，以便：

- 自訂[連線原則](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- 針對與您所在不同的 Azure Cosmos 帳戶執行作業

您可透過[環境變數](#access-the-account-endpoint-and-primary-key-env-variables)來存取目前帳戶的連接字串和主要金鑰。 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>存取帳戶端點和主要金鑰環境變數
您可存取筆記本所在帳戶的內建端點和金鑰。

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> ``COSMOS.ENDPOINT`` 和 ``COSMOS.KEY`` 變數僅適用於 SQL API。 針對其他 API，請在 Azure Cosmos 帳戶的 [連接字串] 或 [金鑰] 刀鋒視窗中尋找端點和金鑰。  

## <a name="reset-notebooks-workspace"></a>重設筆記本工作區
若要將筆記本工作區重設為預設設定，請在命令列上選取 [重設工作區]。 這會移除任何已安裝的自訂套件，並重新啟動 Jupyter 伺服器。 筆記本、檔案和 Azure Cosmos 資源將不受影響。  

![重設筆記本工作區](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點
- 了解[適用於 SQL API 的 Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
