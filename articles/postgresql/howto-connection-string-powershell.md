---
title: 使用 PowerShell 產生連接字串-適用於 PostgreSQL 的 Azure 資料庫
description: 本文提供 Azure PowerShell 範例，以產生連接至適用於 PostgreSQL 的 Azure 資料庫的連接字串。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708164"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>如何使用 PowerShell 產生適用於 PostgreSQL 的 Azure 資料庫連接字串

本文示範如何產生適用於 PostgreSQL 的 Azure 資料庫伺服器的連接字串。 您可以使用連接字串，從許多不同的應用程式連接到適用於 PostgreSQL 的 Azure 資料庫。

## <a name="requirements"></a>需求

本文使用在以下指南中建立的資源作為起點：

* [快速入門：使用 PowerShell 建立適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>取得連接字串

此 `Get-AzPostgreSqlConnectionString` Cmdlet 可用來產生連接字串，以便將應用程式連接至適用於 PostgreSQL 的 Azure 資料庫。 下列範例會從 **mydemoserver**傳回 PHP 用戶端的連接字串。

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

`Client` 參數的有效值包括：

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 PowerShell 自訂適用於 PostgreSQL 的 Azure 資料庫伺服器參數](howto-configure-server-parameters-using-powershell.md)
