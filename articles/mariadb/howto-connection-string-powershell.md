---
title: 使用 PowerShell 產生連接字串 - 適用於 MariaDB 的 Azure 資料庫
description: 本快速入門提供 Azure PowerShell 範例，以產生連線至「適用於 MariaDB 的 Azure 資料庫」的連接字串。
author: savjani
ms.author: pariks
ms.service: jroth
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 79109a7f39debfa14c8a11d9014685382151ab3b
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663718"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>如何使用 PowerShell 產生適用於 MariaDB 的 Azure 資料庫連接字串

本文示範如何產生適用於 MariaDB 的 Azure 資料庫伺服器的連接字串。 您可以使用連接字串，從許多不同的應用程式連線到適用於 MariaDB 的 Azure 資料庫。

## <a name="requirements"></a>需求

本文使用在以下指南中建立的資源作為起點：

* [快速入門：使用 PowerShell 建立適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>取得連接字串

`Get-AzMariaDbConnectionString` Cmdlet 是用來產生連接字串，以便將應用程式連線到適用於 MariaDB 的 Azure 資料庫。 下列範例會從 **mydemoserver** 傳回 PHP 用戶端的連接字串。

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [使用 PowerShell 自訂適用於 MariaDB 的 Azure 資料庫伺服器參數](howto-configure-server-parameters-using-powershell.md)
