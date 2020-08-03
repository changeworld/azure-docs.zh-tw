---
title: 重新開機伺服器-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何使用 Azure CLI 來重新開機適用於 MySQL 的 Azure 資料庫伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9707aaa911fc8ceb8c03b369bd6479646ca3418c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500720"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>使用 Azure CLI 重新開機適用於 MySQL 的 Azure 資料庫伺服器
本主題說明如何重新啟動適用於 MySQL 的 Azure 資料庫伺服器。 您可能會為了進行維護而需要重新啟動伺服器，進而在伺服器執行作業時導致短暫中斷。

如果服務忙碌中，系統會阻止伺服器重新啟動。 例如，該服務可能正在處理先前要求的作業，例如調整虛擬核心。

完成重新啟動所需的時間取決於 MySQL 復原程序。 若要減少重新啟動時間，建議您先盡量減少伺服器上發生的活動數量，再進行重新啟動。

## <a name="prerequisites"></a>Prerequisites
若要完成本操作說明指南，您需要：
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 本操作說明指南會要求您使用 Azure CLI 2.0 版或更新版本。 若要確認版本，請在 Azure CLI 命令提示字元中輸入 `az --version`。 若要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。


## <a name="restart-the-server"></a>重新啟動伺服器

使用下列命令重新開機伺服器：

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟

瞭解[如何在適用於 MySQL 的 Azure 資料庫中設定參數](howto-configure-server-parameters-using-cli.md)
