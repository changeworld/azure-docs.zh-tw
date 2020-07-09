---
title: 自動成長儲存體-Azure CLI-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何使用適用於 MariaDB 的 Azure 資料庫中的 Azure CLI 來啟用自動成長儲存體。
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 39b07e313213be2645d211f292d49d9fa1a8d6bc
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120155"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>使用 Azure CLI 自動成長適用於 MariaDB 的 Azure 資料庫儲存體
本文說明如何在不影響工作負載的情況下，將適用於 MariaDB 的 Azure 資料庫伺服器存放裝置設定為可成長。

[達到儲存空間限制](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)的伺服器會設定為唯讀。 如果已啟用儲存體自動成長，則針對具有小於 100 GB 已布建儲存體的伺服器，布建的儲存體大小會在可用儲存空間低於 1 GB 或10% 的已布建儲存體時，增加 5 GB。 針對具有超過 100 GB 已布建儲存體的伺服器，當可用儲存空間低於布建的儲存體大小的5% 時，布建的儲存體大小會增加5%。 [這裡](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)所指定的最大儲存體限制。

## <a name="prerequisites"></a>必要條件
若要完成本操作說明指南，您需要：
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="enable-mariadb-server-storage-auto-grow"></a>啟用適用于 mariadb 伺服器存放裝置自動成長

使用下列命令，在現有的伺服器上啟用伺服器自動成長儲存體：

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

使用下列命令建立新的伺服器時，啟用伺服器自動成長儲存體：

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>後續步驟

深入瞭解[如何建立計量的警示](howto-alert-metric.md)。