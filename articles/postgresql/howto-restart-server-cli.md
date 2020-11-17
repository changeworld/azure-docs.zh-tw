---
title: 重新開機伺服器-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何使用 Azure CLI 來重新開機適用於 PostgreSQL 的 Azure 資料庫單一伺服器
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647642"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>使用 Azure CLI 重新開機適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
本主題說明如何重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器。 您可能會為了進行維護而需要重新啟動伺服器，進而在伺服器執行作業時導致短暫中斷。

如果服務忙碌中，系統會阻止伺服器重新啟動。 例如，該服務可能正在處理先前要求的作業，例如調整虛擬核心。
 
完成重新啟動所需的時間取決於 PostgreSQL 復原程序。 若要減少重新啟動時間，建議您先盡量減少伺服器上發生的活動數量，再進行重新啟動。

## <a name="prerequisites"></a>先決條件
若要完成本作法指南：
- 建立 [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 2.0 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="restart-the-server"></a>重新啟動伺服器

使用下列命令重新開機伺服器：

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟

瞭解 [如何在適用於 PostgreSQL 的 Azure 資料庫中設定參數](howto-configure-server-parameters-using-cli.md)
