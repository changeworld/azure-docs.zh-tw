---
title: 針對已啟用 Arc 的于 postgresql 超大規模伺服器群組取得連接端點和表單連接字串
titleSuffix: Azure Arc enabled data services
description: 針對已啟用 Arc 的于 postgresql 超大規模伺服器群組取得連接端點和表單連接字串
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 93b0547ba25f6534593a0a016ebfa5cbe4d2be2e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936802"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>針對已啟用 Arc 的于 postgresql 超大規模伺服器群組取得連接端點和表單連接字串

本文說明如何抓取伺服器群組的連接端點，以及如何形成您將搭配應用程式和/或工具使用的連接字串。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>取得連接端點：

### <a name="from-cli-with-azdata"></a>從具有 azdata 的 CLI
#### <a name="1-connect-to-your-arc-data-controller"></a>1. 連接到您的 Arc 資料控制器：
- 如果您已經在 Arc 資料控制器的主機上開啟會話：請執行下列命令：
```console
azdata login
```

- 如果您沒有在 Arc 資料控制器的主機上開啟會話：請執行下列命令 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. 顯示連接端點
執行以下命令：
```console
azdata arc postgres endpoint list -n <server group name>
```
它會傳回輸出，如下所示：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```
使用這些端點：
- 形成您的連接字串，並與您的用戶端工具或應用程式連接
- 從您的瀏覽器存取 Grafana 和 Kibana 儀表板

例如，您可以使用名為 _于 postgresql Instance_ 的端點，將 psql 連接到您的伺服器群組。 例如：
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - 在名為 "_于 postgresql Instance_ " 的結束點中指出之_于 postgresql_使用者的密碼是您在建立伺服器群組時所選擇的密碼。
> - 關於 azdata：與您的連線相關聯的租用會持續約10小時。 之後，您必須重新連接。 如果您的租用已過期，當您嘗試使用 azdata 以外的 (執行命令時，將會收到下列錯誤訊息： azdata 登入) ：_錯誤： (401) _ 
>  _原因：未經授權_的 
>  _HTTP 回應標頭： HTTPHeaderDict ( {' Date '： ' Sun，06 Sep 2020 16:58:38 GMT '，' content-type '： ' 0 '，' WWW-驗證 '： '_ 
>  _Basic realm = "需要登_入認證"，持有者錯誤 = "invalid_token"，error_description = "權杖已過期" '} ) _ 在這種情況下，您必須使用 azdata 重新連接，如上面所述。

## <a name="from-cli-with-kubectl"></a>從具有 kubectl 的 CLI
- 如果您的伺服器群組是 Postgres 第12版 (預設) ，則請執行下列命令：
```console
kubectl get postgresql-12/<server group name>
```
- 如果您的伺服器群組是 Postgres 第11版，則下列命令：
```console
kubectl get postgresql-11/<server group name>
```

這些命令會產生如下所示的輸出。 您可以使用該資訊來形成您的連接字串：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>表單連接字串：
針對您的伺服器群組，請使用下表的連接字串範本。 然後您可以視需要進行複製/貼上和自訂：

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C + + (libpq) 

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>Web 應用程式

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>下一步
- 瞭解如何相應放大 (在您的伺服器群組[) 新增背景工作節點](scale-out-postgresql-hyperscale-server-group.md)
- 深入瞭解向上 [或向下延展 (增加/減少記憶體/虛擬核心) ](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) 伺服器群組


