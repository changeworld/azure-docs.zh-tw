---
title: 自動增長存儲 - Azure CLI - 用於後格雷SQL的 Azure 資料庫 - 單個伺服器
description: 本文介紹如何使用 Azure 資料庫中的 Azure CLI 配置存儲自動增長，用於 PostgreSQL - 單伺服器。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767937"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>自動增長 Azure 資料庫，用於 PostgreSQL 存儲 - 使用 Azure CLI 的單伺服器
本文介紹如何為 PostgreSQL 伺服器存儲配置 Azure 資料庫，以便在不影響工作負載的情況下增長。

[伺服器達到存儲限制](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)，設置為唯讀。 如果啟用了存儲自動增長，則對於預配存儲少於 100 GB 的伺服器，一旦可用存儲低於 1 GB 或預配存儲的 10%，則預配存儲大小將增加 5 GB。 對於具有 100 GB 以上預配存儲的伺服器，當可用存儲空間低於預配存儲大小的 5% 時，預配存儲大小將增加 5%。 [此處](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)指定的最大存儲限制適用。

## <a name="prerequisites"></a>Prerequisites
若要完成本操作說明指南，您需要：
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="enable-postgresql-server-storage-auto-grow"></a>啟用 PostgreSQL 伺服器存儲自動增長

使用以下命令在現有伺服器上啟用伺服器自動增長存儲：

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

啟用伺服器自動增長存儲，同時使用以下命令創建新伺服器：

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>後續步驟

瞭解如何[在指標上創建警報](howto-alert-on-metric.md)。