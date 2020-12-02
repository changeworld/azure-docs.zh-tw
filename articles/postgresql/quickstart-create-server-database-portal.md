---
title: 快速入門：建立伺服器 - Azure 入口網站 - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 在本快速入門指南中，您將使用 Azure 入口網站來建立和管理適用於 PostgreSQL 的 Azure 資料庫伺服器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492381"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立適用於 PostgreSQL 的 Azure 資料庫伺服器

Azure Database for PostgreSQL 是一種受控服務，您用來在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 本快速入門說明如何建立適用於 PostgreSQL 的 Azure 資料庫伺服器並連線。

## <a name="prerequisites"></a>Prerequisites
需要有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器
移至 [Azure 入口網站](https://portal.azure.com/)建立適用於 PostgreSQL 的 Azure 資料庫單一伺服器資料庫。 搜尋並選取 [適用於 PostgreSQL 的 Azure 資料庫伺服器]。

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="尋找適用於 PostgreSQL 的 Azure 資料庫。":::

1. 選取 [新增]  。

2. 在「建立適用於 PostgreSQL 的 Azure 資料庫」頁面上，選取 [單一伺服器]。

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="選取單一伺服器":::

3. 在 **基本資料** 表單中輸入下列資訊。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="螢幕擷取畫面：顯示用於建立單一伺服器的 [基本資料] 索引標籤。":::

   |設定|建議的值|描述|
   |:---|:---|:---|
   |訂用帳戶|您的訂用帳戶名稱|選取所需的 Azure 訂用帳戶。|
   |資源群組|*myresourcegroup*| 新的資源群組名稱，或您訂用帳戶中現有的資源群組名稱。|
   |伺服器名稱 |*mydemoserver*|可識別 Azure Database for PostgreSQL 伺服器的唯一名稱。 網域名稱 postgres.database.azure.com 已附加至您提供的伺服器名稱。 伺服器只能包含小寫字母、數字及連字號 (-) 字元。 其必須包含 3 到 63 個字元。|
   |資料來源 | None | 選取 [無]  從頭建立新的伺服器。 只有當您從現有伺服器的異地備份還原時，才選取 [備份]。|
   |系統管理員使用者名稱 |myadmin | 輸入您的伺服器管理使用者名稱， 不能以 **pg_** 開頭，且不允許使用這些值：**azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest** 或 **public**。|
   |密碼 |您的密碼| 伺服器管理使用者的新密碼。 其必須包含 8 到 128 個字元，且來自下列類別的其中三個類別：英文大寫字母、英文小寫字母、數字 (0 到 9) 及非英數字元 (例如 !、$、#、%)。|
   |Location|您想要的位置| 從下拉式清單中選取位置。|
   |版本|最新的主要版本| 最新 PostgreSQL 主要版本 (除非您有其他特定需求)。|
   |計算 + 儲存體 | 「使用預設值」| 預設定價層是 **一般用途** 搭配 **4 個虛擬核心** 以及 **100 GB** 儲存體。 備份保留期設定為 **7 天**，並具備 **異地備援** 備份選項。<br/>了解[定價](https://azure.microsoft.com/pricing/details/postgresql/server/)並視需要更新預設值。|


   > [!NOTE]
   > 如果您的工作負載只需要輕量計算和 I/O，請考慮使用基本定價層。 請注意，在基本定價層中建立的伺服器後續無法調整為「一般用途」或「記憶體最佳化」。

5. 選取 [檢閱 + 建立] 以檢閱您的選項。 選取 [建立] 以佈建伺服器。 完成此作業可能需要幾分鐘的時間。
    > [!NOTE]
    > 系統會建立空的資料庫 **postgres**。 您也會發現用來分隔受管理服務處理序與使用者動作的 **azure_maintenance** 資料庫。 您無法存取 **azure_maintenance** 資料庫。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="部署成功。":::

[有任何問題嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>設定防火牆規則
根據預設，您建立的伺服器不是可公開存取的。 您必須向 IP 位址授與權限。 請移至 Azure 入口網站中的伺服器資源，然後從伺服器資源的左側功能表選取 [連線安全性]。 如果您不確定如何尋找您的資源，請參閱[開啟資源](../azure-resource-manager/management/manage-resources-portal.md#open-resources)。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="顯示連線安全性防火牆規則的螢幕擷取畫面。":::

選取 [新增目前的用戶端 IP 位址]，然後選取 [儲存]。 您可以新增更多 IP 位址或提供 IP 範圍，以便從這些 IP 位址連線到您的伺服器。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的防火牆規則](./concepts-firewall-rules.md)。

> [!NOTE]
> 若要避免連線問題，請檢查您的網路是否允許透過連接埠 5432 的輸出流量。 適用於 PostgreSQL 的 Azure 資料庫會使用該連接埠。

[有任何問題嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>使用 psql 連線到伺服器

您可以使用 [psql](http://postgresguide.com/utilities/psql.html) 或 [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)，這些都是熱門的 PostgreSQL 用戶端。 在本快速入門中，我們將使用 Azure 入口網站內 [Azure Cloud Shell](../cloud-shell/overview.md) 中的 psql 進行連線。

1. 請記下您伺服器的 [概觀] 區段中新建立之伺服器的伺服器名稱、伺服器管理員登入名稱、密碼以及訂用帳戶識別碼。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="取得連線資訊。":::


2. 藉由選取左上方的圖示，在入口網站中開啟 Azure Cloud Shell。

   > [!NOTE]
   > 如果您是第一次啟動 Cloud Shell，則會看到要您建立資源群組和儲存體帳戶的提示。 這是一次性的步驟，而且會針對所有工作階段自動連接。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="螢幕擷取畫面：顯示用於開啟 Azure Cloud Shell 的伺服器資訊和圖示。":::

3. 在 Azure Cloud Shell 終端機內執行下列命令。 將值取代為實際的伺服器名稱和管理員使用者登入名稱。 以下列格式使用空白資料庫 **postgres** 和管理使用者：`<admin-username>@<servername>`。

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   以下是 Cloud Shell 終端機中的體驗外觀：

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. 在相同的 Azure Cloud Shell 終端機中，建立名為 **客體** 的資料庫。

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. 將連線切換至新建立的 **客體** 資料庫。

   ```bash
   \c guest
   ```
6. 輸入 `\q`，然後選取 Enter 鍵關閉 psql。

[有任何問題嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>清除資源
您已成功在資源群組中建立適用於 PostgreSQL 的 Azure 資料庫伺服器。 如果您覺得未來不需要這些資源，則可以藉由刪除資源群組或 PostgreSQL 伺服器來刪除這些資源。

若要刪除資源群組：

1. 在 Azure 入口網站中，搜尋並選取 [資源群組]。
2. 在 [資源群組] 清單中，選擇資源群組的名稱。
3. 在資源群組的 [概觀] 頁面中，選取 [刪除資源群組]。
4. 在確認對話方塊中輸入您的資源群組名稱，然後選取 [刪除]。

若要刪除伺服器，請在伺服器的 [概觀] 頁面上，選取 [刪除] 按鈕：

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="螢幕擷取畫面：顯示用於刪除伺服器的按鈕。":::

[有任何問題嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [設計資料庫](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[找不到您要找的項目嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)