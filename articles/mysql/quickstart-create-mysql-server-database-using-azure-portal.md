---
title: 快速入門：建立伺服器 - Azure 入口網站 - 適用於 MySQL 的 Azure 資料庫
description: 本文逐步引導您使用 Azure 入口網站在五分鐘內快速建立範例 Azure Database for MySQL 伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906553"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立適用於 MySQL 的 Azure 資料庫伺服器

適用於 MySQL 的 Azure 資料庫是一個受控服務，您可用來在雲端執行、管理及調整高可用性 MySQL 資料庫。 本快速入門說明如何使用 Azure 入口網站，在大約五分鐘內建立適用於 MySQL 的 Azure 資料庫伺服器。  

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟 Web 瀏覽器，然後移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器
您可使用一組已定義的[計算和儲存體](./concepts-compute-unit-and-storage.md)資源來建立 Azure Database for MySQL 伺服器。 您可在 [Azure 資源群組](../azure-resource-manager/management/overview.md)內建立伺服器。

請依照下列步驟來建立 Azure Database for MySQL 伺服器：

1. 在入口網站的左上角，選取 [建立資源]  (+)。

2. 選取 [資料庫]   > [Azure Database for MySQL]  。 您也可以在搜尋方塊中輸入 **MySQL** 以尋找此服務。

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="適用於 MySQL 的 Azure 資料庫 選項":::

3. 在新伺服器詳細資料表單中填寫下列資訊︰
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="建立伺服器表單":::

**設定** | **建議的值** | **欄位描述** 
---|---|---
訂用帳戶 | 您的訂用帳戶 | 選取您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的訂用帳戶。
資源群組 | *myresourcegroup* | 提供新的或現有的資源群組名稱。 您可以使用資源群組來組織屬於單一專案的相依性。
伺服器名稱 | 唯一的伺服器名稱 | 輸入可識別 Azure Database for MySQL 伺服器的唯一名稱。 例如 'mysqldbserver'。伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
資料來源 |*None* | 選取 [無]  從頭建立新的伺服器。 (如果您要從現有「適用於 MySQL 的 Azure 資料庫」伺服器的異地備份建立伺服器，可以選取 [備份])。
伺服器管理員登入 | myadmin | 輸入伺服器管理員的使用者名稱。 您不能使用 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public** 作為管理使用者名稱。
密碼 | 您的選擇 | 為伺服器管理帳戶提供新密碼。 密碼的長度必須為 8 到 128 個字元，且包含大寫或小寫字母、數字和非英數字元 (!、$、#、% 等等) 的組合。
確認密碼 | 您的選擇| 確認管理帳戶密碼。
Location | *最接近使用者的區域*| 選擇最靠近您的使用者或其他 Azure 應用程式的位置。
版本 | *最新主要版本*| 最新主要版本 (除非您有需要另一個版本的特定需求)。
計算 + 儲存體 | **一般用途**、**Gen 5**、**2 個虛擬核心**、**5 GB**、**7 天**、**異地備援** |新伺服器的計算、儲存體和備份組態。 選取 [設定伺服器]  。 接下來，選取適當的定價層；如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/mysql/)。 若要啟用異地備援儲存體中的伺服器備份，請從 [備份備援選項] 中選取 [異地備援]。 選取 [確定]。

   > [!NOTE]
   > 如果您的工作負載只需要輕量計算和 I/O，請考慮使用基本定價層。 請注意，在基本定價層中建立的伺服器後續無法調整為「一般用途」或「記憶體最佳化」。 

4. 選取 [檢閱+建立] 以佈建伺服器。 佈建作業可能需要多達 20 分鐘的時間。
   
5. 在工具列上選取 [通知] \(鈴鐺圖示) 以監視部署程序。
   
根據預設，您的伺服器底下會建立下列資料庫：**information_schema**、**mysql**、**performance_schema** 和 **sys**。

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則
根據預設，建立的伺服器受防火牆保護，且不可公開存取。 若要授與 IP 存取權，請移至 Azure 入口網站中的伺服器資源，然後從伺服器資源的左側功能表選取 [連線安全性]。 如果不知道如何尋找資源，請參閱[如何開啟資源](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)。

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="連線安全性 - 防火牆規則":::
   
現在，選取 [新增目前的用戶端 IP 位址]，然後選取 [儲存]。 您可以新增其他 IP 或提供 IP 範圍，以從這些 IP 連線到您的伺服器。 如需詳細資訊，請參閱[如何在適用於 MySQL 的 Azure 資料庫上管理防火牆規則](./concepts-firewall-rules.md)

> [!NOTE]
> 檢查您的網路是否允許連接埠 3306 的輸出流量，以供適用於 MySQL 的 Azure 資料庫使用以避免連線問題。  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>使用 MySQL 命令列用戶端連線到適用於 MySQL 的 Azure 資料庫伺服器
您可以從本機環境中選擇 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 來連線到伺服器。 在本快速入門中，我們將在 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 中執行 **mysql.exe** 以連線到伺服器。

1. 選取左上方反白顯示的圖示，在入口網站中啟動 Azure Cloud Shell。 請記下**概觀**區段中新建立之伺服器的伺服器名稱、伺服器管理員登入名稱、密碼以及訂用帳戶，如下圖所示。

    >[!NOTE]
    >如果您是第一次啟動 Cloud Shell，會看到建立資源群組、儲存體帳戶的提示。 這是一次性的步驟，而且會針對所有工作階段自動連接。 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="入口網站完整檢視 Cloud Shell":::
2. 在 Azure Cloud Shell 終端機上執行此命令。 將值取代為實際的伺服器名稱和管理員使用者登入名稱。 適用於 MySQL 的 Azure 資料庫管理員使用者名稱需要 '@\<servername>'，如下所示  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  以下是 Cloud Shell 終端機中體驗顯示的樣子
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. 在相同的 Azure Cloud Shell 終端機中，建立資料庫**客體** 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. 變更為資料庫**來賓**
  ```
  mysql> USE guest;
  Database changed 
  ```
5. 輸入 ```quit```，然後選取 Enter 鍵結束 mysql。   

## <a name="clean-up-resources"></a>清除資源
您已成功在資源群組中建立適用於 MySQL 的 Azure 資料庫伺服器。  如果您覺得未來不需要這些資源，可以刪除資源群組，或刪除 MySQL 伺服器。 若要移除資源群組，請依照這些步驟操作：
1. 在 Azure 入口網站中，搜尋並選取 [資源群組]。 
2. 在 [資源群組] 清單中，選擇資源群組的名稱。
3. 在資源群組的 [概觀]頁面中，選取 [刪除資源群組]。
4. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

若要刪除伺服器，您可以在伺服器的 [概觀] 頁面上按一下 [刪除] 按鈕，如下所示：
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="刪除資源":::

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上建置 PHP 應用程式](../app-service/app-service-web-tutorial-php-mysql.md)
>[使用 MySQL 在 Linux 上建置 PHP 應用程式](../app-service/containers/tutorial-php-mysql-app.md)
>[ MySQL 建置以 Java 為基礎的 Spring 應用程式](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
