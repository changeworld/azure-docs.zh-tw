---
title: 基礎結構雙重加密-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何為您的適用於 MySQL 的 Azure 資料庫設定及管理基礎結構雙重加密。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: eafad5edf9dcac5745986d09060baf7e4278762d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903979"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫的基礎結構雙重加密

瞭解如何使用設定和管理適用於 MySQL 的 Azure 資料庫的基礎結構雙重加密。

## <a name="prerequisites"></a>必要條件

* 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>使用基礎結構雙重加密建立適用於 MySQL 的 Azure 資料庫伺服器-入口網站

請遵循下列步驟，以從 Azure 入口網站的基礎結構雙重加密建立適用於 MySQL 的 Azure 資料庫伺服器：

1. 在入口網站的左上角，選取 [建立資源]  (+)。

2. 選取 [資料庫]   > [Azure Database for MySQL]  。 您也可以在搜尋方塊中輸入 **MySQL** 以尋找此服務。

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="適用於 MySQL 的 Azure 資料庫 選項":::

3. 提供伺服器的基本資訊。 選取 [ **其他設定** ] 並啟用 [ **基礎結構雙重加密** ] 核取方塊，以設定參數。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="適用於 MySQL 的 Azure 資料庫選取專案":::

4. 選取 [檢閱+建立] 以佈建伺服器。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="適用於 MySQL 的 Azure 資料庫摘要":::

5. 建立伺服器之後，您可以藉由檢查 **資料加密** 伺服器分頁中的狀態，驗證基礎結構雙重加密。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="適用於 MySQL 的 Azure 資料庫驗證":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>使用基礎結構雙重加密建立適用於 MySQL 的 Azure 資料庫伺服器-CLI

遵循下列步驟，從 CLI 建立具有基礎結構雙重加密的適用於 MySQL 的 Azure 資料庫伺服器：

此範例會在位置中建立名為的資源群組 `myresourcegroup` `westus` 。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
下列範例會使用伺服器系統管理員登入 `myadmin` 在 `myresourcegroup` 您的資源群組中建立且名為 `mydemoserver` 的 MySQL 5.7 伺服器 (位於美國西部)。 這是**第 4 代** **一般用途**伺服器，它具有 **2 個 vCore**。 這也會為所建立的伺服器啟用基礎結構雙重加密。 將 `<server_admin_password>` 替換成您自己的值。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>下一步

 若要深入瞭解資料加密，請參閱 [適用於 MySQL 的 Azure 資料庫資料基礎結構雙重加密](concepts-Infrastructure-double-encryption.md)。
