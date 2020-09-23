---
title: 快速入門：建立伺服器 - Azure 入口網站 - 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器
description: 本快速入門指南說明如何使用 Azure 入口網站使用者介面，建立及管理「適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器」。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 25c3f875717c9c064af0ce27bdab735db67d2f5d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943595"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

Azure Database for PostgreSQL 是一種受控服務，您用來在雲端執行、管理及調整高可用性的 PostgreSQL 資料庫。 本快速入門說明如何使用 Azure 入口網站，在大約五分鐘內建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

開啟 Web 瀏覽器，然後移至[入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

「適用於 PostgreSQL 的 Azure 資料庫」伺服器是以一組已設定的[計算和儲存體資源](./concepts-compute-storage.md)所建立。 伺服器會建立在 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)內。

若要建立 Azure Database for PostgreSQL 伺服器，請採取下列步驟：

1. 在入口網站的左上角，選取 [建立資源]  (+)。

2. 選取 [資料庫]   > [Azure Database for PostgreSQL]  。

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="選單中的適用於 PostgreSQL 的 Azure 資料庫":::

3. 選取 [彈性伺服器] 部署選項。

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="選取適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器部署選項":::

4. 在 [基本資料]  表單中填寫下列資訊：

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="建立伺服器":::

    設定|建議的值|描述
    ---|---|---
    訂用帳戶|您的訂用帳戶名稱|您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的訂用帳戶。
    資源群組|*myresourcegroup*| 新的資源群組名稱，或您訂用帳戶中現有的資源群組名稱。
    伺服器名稱 |*mydemoserver*|可識別 Azure Database for PostgreSQL 伺服器的唯一名稱。 網域名稱 postgres.database.azure.com  已附加至您提供的伺服器名稱。 伺服器只能包含小寫字母、數字及連字號 (-) 字元。 它必須包含至少 3 到 63 個字元。
    系統管理員使用者名稱 |myadmin | 連線至伺服器時所要使用之自己的登入帳戶。 管理員登入名稱不能是 **azure_superuser**、**azure_pg_admin** **admin** **administrator** **root** **guest** 或 **public**。 它不能以 **pg_** 開頭。
    密碼 |您的密碼| 伺服器管理帳戶的新密碼。 其必須包含 8 到 128 個字元。 您的密碼必須包含下列類別中三種類別的字元：英文大寫字母、英文小寫字母、數字 (0 到 9) 及非英數字元 (!、$、#、% 等等)。
    Location|最接近使用者的區域| 最靠近您使用者的位置。
    版本|最新的主要版本| 最新 PostgreSQL 主要版本 (除非您有其他特定需求)。
    計算 + 儲存體 | **一般用途**、**4 虛擬核心**、**512 GB**、**7 天** | 新伺服器的計算、儲存體和備份組態。 選取 [設定伺服器]  。 一般用途、4 虛擬核心、512 GB 和 7 天分別是**計算層**、**虛擬核心**、**儲存體**和**備份保留期間**的預設值。 您可以讓這些滑桿保持原狀或加以調整。 若要儲存此定價層選取項目，請選取 [確定]  。 下方螢幕擷取畫面會擷取這些選取項目。

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="定價層窗格":::
    
5. 設定網路功能選項

    在網路功能索引標籤上，您可以選擇伺服器的連線方式。 Azure Database for PostgreSQL 會在伺服器層級建立防火牆。 它會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非您建立規則以針對特定 IP 位址開啟防火牆。 建議您讓伺服器可公開存取：

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="網路功能窗格":::

    然後將其限制為您自己的用戶端 IP 位址：

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="選取「新增目前的用戶端 IP 位址」":::

6. 選取 [檢閱 + 建立] 以檢閱您的選項。 選取 [建立] 以佈建伺服器。 這項作業可能需要幾分鐘的時間。

7. 在工具列上，選取 [通知]  圖示 (鈴鐺) 以監視部署程序。 完成部署後，您可以選取 [釘選到儀表板]  ，在 Azure 入口網站儀表板上建立此伺服器的圖格，以作為伺服器 [概觀]  頁面的捷徑。 選取 [移至資源]**** 會開啟伺服器的 [概觀]**** 頁面。

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="通知窗格":::

   根據預設，**postgres** 資料庫會建立在您的伺服器底下。 [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) 資料庫是要供使用者、公用程式及第三方應用程式使用的預設資料庫。 (其他預設資料庫是 **azure_maintenance**。 其功能是分隔受控服務處理程序和使用者動作。 您無法存取此資料庫。)

    > [!NOTE]
    > 您的 Azure Database for PostgreSQL 伺服器連線會透過連接埠 5432 通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 5432 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 5432，否則您無法連線到您的伺服器。
    >

## <a name="get-the-connection-information"></a>取得連線資訊

當您建立 Azure Database for PostgreSQL 伺服器時，系統會建立名為 **postgres** 的預設資料庫。 若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您稍早可能已在快速入門文章中記下這些值。 若未這麼做，您可以在 Azure 入口網站的伺服器 [概觀]**** 頁面輕鬆尋找伺服器名稱和登入資訊。

開啟伺服器的 [概觀]**** 頁面。 記下 [伺服器名稱]**** 和 [伺服器管理員登入名稱]****。 將您的游標停留在每個欄位上，複製符號就會出現在文字右邊。 視需要選取複製符號來複製值。

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="伺服器概觀頁面":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>使用 psql 連線至 PostgreSQL 資料庫

您可以使用一些應用程式來連線到您的 Azure Database for PostgreSQL 伺服器。 如果您的用戶端電腦已安裝 PostgreSQL，您可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本機執行個體來連線到 Azure PostgreSQL 伺服器。 現在我們將使用 psql 命令列公用程式來連線到 Azure PostgreSQL 伺服器。

1. 執行下列 psql 命令以連線到「適用於 PostgreSQL 的 Azure 資料庫」伺服器

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 **mydemoserver.postgres.database.azure.com** 上名為 **postgres** 的預設資料庫。 系統提示輸入密碼時，請輸入您選擇的 `<server_admin_password>`。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   連線之後，psql 公用程式會顯示 postgres 提示字元，供您鍵入 sql 命令。 在初始連線輸出中，可能會因為您所使用的 psql 版本與適用於 PostgreSQL 的 Azure 資料庫伺服器版本不同，而顯示警告。

   psql 輸出範例：

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > 如果未將防火牆設定為允許用戶端的 IP 位址，就會發生下列錯誤：
   >
   > "psql:FATAL: no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL：需要 SSL 連線。 指定 SSL 選項，然後再試一次。
   >
   > 請確認上述防火牆規則步驟中允許您用戶端的 IP。

2. 在提示字元輸入下列命令以建立名為 "mypgsqldb" 的空白資料庫︰

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. 在提示字元，執行下列命令將連線切換到新建立的資料庫 **mypgsqldb**：

    ```bash
    \c mypgsqldb
    ```

4. 輸入 `\q`，然後選取 Enter 鍵結束 psql。

您已透過 psql 連線至適用於 PostgreSQL 的 Azure 資料庫伺服器，並已建立空的使用者資料庫。

## <a name="clean-up-resources"></a>清除資源

您有兩種方式可以清除您在本快速入門中建立的資源。 您可以刪除 Azure 資源群組，其中包括資源群組中的所有資源。 如果您想要讓其他資源保持不變，則只刪除該伺服器資源。

> [!TIP]
> 此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行快速入門，請勿清除您在此快速入門中建立的資源。 如果您不打算繼續，請遵循下列步驟，在入口網站中刪除本快速入門所建立的資源。

若要刪除整個資源群組 (包括新建立的伺服器)：

1. 在入口網站中找出您的資源群組。 在左側功能表中，選取 [資源群組]****。 然後選取您的資源群組名稱，例如範例中的 **myresourcegroup**。

2. 在資源群組頁面上，選取 [刪除]  。 在文字方塊中輸入您的資源群組名稱 (例如範例中的 **myresourcegroup**)，以確認刪除。 選取 [刪除]  。

若要刪除新建立的伺服器：

1. 請在入口網站中找出您的伺服器 (如果您未將它開啟)。 在左側功能表上選取 [所有資源]****。 然後搜尋您所建立的伺服器。

2. 在 [概觀]  頁面上，按一下 [刪除]  。

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="刪除按鈕":::

3. 確認您要刪除的伺服器名稱，並且會檢視其下受影響的資料庫。 在文字方塊中輸入您的伺服器名稱，例如範例中的 **mydemoserver**。 選取 [刪除]  。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 App Service 和 PostgreSQL 部署 Django 應用程式](tutorial-django-app-service-postgres.md)
