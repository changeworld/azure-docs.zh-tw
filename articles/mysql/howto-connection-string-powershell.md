---
title: 使用 PowerShell 產生連接字串 - 適用於 MySQL 的 Azure 資料庫
description: 本文提供 Azure PowerShell 範例，以產生連線至適用於 MySQL 的 Azure 資料庫的連接字串。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9f960d32dcf1f359327dccc01eeb06825cc3a062
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541465"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>如何使用 PowerShell 產生適用於 MySQL 的 Azure 資料庫連接字串

本文示範如何產生適用於 MySQL 的 Azure 資料庫伺服器的連接字串。 您可以使用連接字串，從許多不同的應用程式連線到適用於 MySQL 的 Azure 資料庫。

## <a name="requirements"></a>需求

本文使用在以下指南中建立的資源作為起點：

* [快速入門：使用 PowerShell 建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>取得連接字串

`Get-AzMySqlConnectionString` Cmdlet 是用來產生連接字串，以便將應用程式連線到適用於 MySQL 的 Azure 資料庫。 下列範例會從 **mydemoserver** 傳回 PHP 用戶端的連接字串。

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [使用 PowerShell 自訂適用於 MySQL 的 Azure 資料庫伺服器參數](howto-configure-server-parameters-using-powershell.md)
