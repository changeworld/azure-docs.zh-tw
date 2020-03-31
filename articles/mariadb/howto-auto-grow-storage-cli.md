---
title: 自動增長存儲 - Azure CLI - MariaDB 的 Azure 資料庫
description: 本文介紹如何使用 MariaDB Azure 資料庫中的 Azure CLI 啟用自動擴展存儲。
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4be84c750f6a3ca7a0d48aa2b98d75272c1cbadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529079"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>使用 Azure CLI 自動增長 MariaDB 存儲的 Azure 資料庫
本文介紹如何為 MariaDB 伺服器存儲配置 Azure 資料庫，以便在不影響工作負荷的情況下增長。

[伺服器達到存儲限制](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)，設置為唯讀。 如果啟用了存儲自動增長，則對於預配存儲少於 100 GB 的伺服器，一旦可用存儲低於 1 GB 或預配存儲的 10%，則預配存儲大小將增加 5 GB。 對於具有 100 GB 以上預配存儲的伺服器，當可用存儲空間低於預配存儲大小的 5% 時，預配存儲大小將增加 5%。 [此處](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)指定的最大存儲限制適用。

## <a name="prerequisites"></a>Prerequisites
若要完成本操作說明指南，您需要：
- [MariaDB 伺服器的 Azure 資料庫](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="enable-mariadb-server-storage-auto-grow"></a>啟用 MariaDB 伺服器存儲自動增長

使用以下命令在現有伺服器上啟用伺服器自動增長存儲：

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

啟用伺服器自動增長存儲，同時使用以下命令創建新伺服器：

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>後續步驟

瞭解如何[在指標上創建警報](howto-alert-metric.md)。