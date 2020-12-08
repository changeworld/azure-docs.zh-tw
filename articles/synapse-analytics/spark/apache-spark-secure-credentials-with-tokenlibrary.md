---
title: 針對 Azure Synapse Analytics，使用已連結的服務保護 Apache Spark 中的存取認證
description: 本文提供有關如何使用已連結的服務和權杖程式庫，安全地將 Azure Synapse Analytics 的 Apache Spark 與其他服務整合的概念
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 8c2e2327667ccb9284a22e65418a80f3066d22df
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510789"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>使用 TokenLibrary 透過連結服務保護您的認證

從外部來源存取資料是常見的模式。 除非外部資料源允許匿名存取，否則您可能需要使用認證、祕密或連接字串來保護您的連線。  

根據預設，Synapse 會使用 Azure Active Directory (AAD) 傳遞，在資源之間進行驗證。  如果您需要使用其他認證連線至資源，請直接使用 TokenLibrary。  TokenLibrary 可簡化對儲存在連結服務或 Azure Key Vault 中的 SAS 權杖、AAD 權杖、連接字串和秘密進行擷取的程序。

從 Azure Key Vault 擷取秘密時，建議您建立 Azure Key Vault 的連結服務。  請確定 Synapse 工作區的受控服務識別 (MSI) 具有 Azure Key Vault 的「秘密取得」權限。  Synapse 會使用 Synapse 工作區的受控服務識別向 Azure Key Vault 進行驗證。 如果您直接連線至沒有連結服務的 Azure Key Vault，您將使用使用者 Azure Active Directory 認證進行驗證。

如需詳細資訊，請參閱[已連結的服務](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="usage"></a>使用量

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
此函式會顯示 TokenLibrary 的說明文件。

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的 TokenLibrary

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 主要儲存體

從主要 Azure Data Lake Storage 存取檔案時，依預設會使用 Azure Active Directory 傳遞進行驗證，而不需要明確使用 TokenLibrary。

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>具有連結服務的 ADLS Gen2 儲存體

Synapse 提供連線至 Azure Data Lake Storage Gen2 時的整合式連結服務體驗。  連結服務可以設定為使用 **帳戶金鑰**、**服務主體**、**受控識別** 或 **認證** 進行驗證。

當連結服務驗證方法設定為 [帳戶金鑰] 時，連結服務將會使用提供的儲存體帳戶金鑰進行驗證，並要求 SAS 金鑰，然後使用 **LinkedServiceBasedSASProvider** 自動將其套用至儲存體要求。

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

當連結服務驗證方法設定為 [受控識別] 或 [服務主體] 時，連結服務會將受控識別或服務主體權杖用於 **LinkedServiceBasedTokenProvider** 提供者。  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 儲存體 (不具連結服務)

使用 SAS 金鑰直接連線至 ADLS Gen2 儲存體，使用 **ConfBasedSASProvider**，並將 SAS 金鑰提供到 **spark.storage.synapse.sas** 組態設定。

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>具有 Azure Key Vault 的 ADLS Gen2 儲存體

使用儲存在 Azure Key Vault 秘密中的 SAS 權杖，連線至 ADLS Gen2 儲存體。  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>其他連結服務的 TokenLibrary

若要連線到其他已連結的服務，您可以直接呼叫 TokenLibrary。

#### <a name="getconnectionstring"></a>getConnectionString()

 若要取出連接字串，請使用 **getConnectionString** 函式，並傳入 **已連結的服務名稱**。

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

getConnectionStringAsMap 是 Scala 和 Python 中提供的協助程式函式，可從連接字串中的 _key=value_ 配對剖析特定值，例如

_`DefaultEndpointsProtocol=https;AccountName=\<ACCOUNT NAME>;AccountKey=\<ACCOUNT KEY>`_

使用 **getConnectionStringAsMap** 函式，並傳遞金鑰來傳回值。  在上述連接字串範例中， 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

會傳回

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

若要從 Azure Key Vault 擷取儲存的秘密，建議您在 Synapse 工作區中建立 Azure Key Vault 的連結服務。 必須為 Synapse 工作區的受控服務識別授與 Azure Key Vault 的 **取得秘密** 權限。  連結服務會使用受控服務識別連線至 Azure Key Vault 服務，以取得秘密。  否則，在直接連線至 Azure Key Vault 時，將會採用使用者的 Azure Active Directory (AAD) 認證。  在此情況下，使用者將必須獲得 Azure Key Vault 中的「取得秘密」權限。

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

若要從 Azure Key Vault 擷取秘密，請使用 **TokenLibrary.getSecret()** 函式。

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>後續步驟

- [寫入專用 SQL 集區](./synapse-spark-sql-pool-import-export.md)
