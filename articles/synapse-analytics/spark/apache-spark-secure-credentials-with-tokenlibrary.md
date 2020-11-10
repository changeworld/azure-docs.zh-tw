---
title: 針對 Azure Synapse Analytics，使用已連結的服務保護 Apache Spark 中的存取認證
description: 本文提供有關如何使用已連結的服務和權杖程式庫，安全地將 Synapse Analytics 的 Apache Spark 與其他服務整合的概念
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 1cdb010e34674d52ebe2135ad1591a163a078708
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324947"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>使用 TokenLibrary 透過已連結的服務保護您的認證
從外部來源存取資料是常見的模式。 除非外部資料源允許匿名存取，否則您可能需要使用認證、祕密或連接字串來保護您的連線。  

Azure Synapse Analytics 提供已連結的服務，藉由將連線詳細資料儲存在已連結的服務或 Azure Key Vault 中，進而簡化整合程序。 建立連結服務後，Apache Spark 就可以參考連結服務，以在您的程式碼中套用連線資訊。 

如需詳細資訊，請參閱[已連結的服務](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
> [!NOTE]
> 從工作區中的 Azure Data Lake Storage 存取檔案會使用 AAD 傳遞進行驗證，因此您不需要使用 TokenLibrary。 


## <a name="prerequisite"></a>必要條件
* 連結的服務 - 您必須建立外部資料源的已連結服務，並從權杖程式庫參考已連結的服務。 深入了解[已連結的服務](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>連線至 Synapse 工作區以外的 ADLS Gen2

Synapse 提供 Azure Data Lake Storage Gen2 的整合已連結的服務體驗。

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>使用權杖程式庫

若要連線到其他已連結的服務，您可以直接呼叫 TokenLibrary。

### <a name="getconnectionstring"></a>GetConnectionString
 若要取出連接字串，請使用 <b>getConnectionString</b> 函式，並傳入<b>已連結的服務名稱</b>。

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
若要從連接字串中的 <i>key=value</i> 配對中剖析特定值，例如 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

使用 <b>getConnectionStringAsMap</b> 函式，並傳遞金鑰來傳回值。
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>後續步驟

- [寫入專用 SQL 集區](./synapse-spark-sql-pool-import-export.md)

