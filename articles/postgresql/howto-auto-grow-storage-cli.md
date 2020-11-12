---
title: 自動成長儲存體-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的 Azure CLI 來設定儲存體自動成長。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b6f3875af4c7f0eace327d810c632a6bb217092
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534189"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>自動成長適用於 PostgreSQL 的 Azure 資料庫儲存體-使用 Azure CLI 的單一伺服器
本文說明如何設定適用於 PostgreSQL 的 Azure 資料庫 server 儲存體在不影響工作負載的情況下成長。

[到達儲存體限制](./concepts-pricing-tiers.md#reaching-the-storage-limit)的伺服器會設定為唯讀。 如果已啟用「儲存體自動成長」，則在已布建儲存體低於 100 GB 的伺服器上，布建的儲存體大小會在可用儲存體低於 1 GB 或10% 的布建儲存體時，立即增加 5 GB。 針對具有超過 100 GB 布建儲存體的伺服器，當可用儲存空間低於所布建儲存體大小的5% 時，布建的儲存體大小會增加5%。 在 [這裡](./concepts-pricing-tiers.md#storage) 指定的最大儲存體限制適用。

## <a name="prerequisites"></a>必要條件

- 您需要 [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-azure-cli.md)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 2.0 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="enable-postgresql-server-storage-auto-grow"></a>啟用于 postgresql 伺服器儲存體自動成長

使用下列命令，在現有伺服器上啟用伺服器自動成長儲存體：

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

使用下列命令來建立新的伺服器時，啟用伺服器自動成長儲存體：

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>後續步驟

瞭解 [如何建立計量警示](howto-alert-on-metric.md)。