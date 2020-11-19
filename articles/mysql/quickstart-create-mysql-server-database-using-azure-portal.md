---
title: 快速入門：建立伺服器 - Azure 入口網站 - 適用於 MySQL 的 Azure 資料庫
description: 本文逐步引導您使用 Azure 入口網站在五分鐘內建立範例 Azure Database for MySQL 伺服器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 4282294ff54fd3da3f764f53efc8b040b9522191
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542247"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Azure Database for MySQL 伺服器

適用於 MySQL 的 Azure 資料庫是一個受控服務，您可用來在雲端執行、管理及調整高可用性 MySQL 資料庫。 本快速入門說明如何使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫單一伺服器。 此外也會說明如何連線至伺服器。

## <a name="prerequisites"></a>Prerequisites
需要有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-database-for-mysql-single-server"></a>建立適用於 MySQL 的 Azure 資料庫單一伺服器
1. 移至 [Azure 入口網站](https://portal.azure.com/)，建立 MySQL 單一伺服器資料庫。 搜尋並選取 [適用於 MySQL 的 Azure 資料庫]：

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="尋找適用於 MySQL 的 Azure 資料庫":::

1. 選取 [新增]  。

2. 在 [選取適用於 MySQL 的 Azure 資料庫部署選項] 頁面上，選取 [單一伺服器]：
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="顯示 [單一伺服器] 選項的螢幕擷取畫面。":::

3. 輸入新單一伺服器的基本設定：

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="顯示 [建立 MySQL 伺服器] 頁面的螢幕擷取畫面。":::

   **設定** | **建議的值** | **說明**
   ---|---|---
   訂用帳戶 | 您的訂用帳戶 | 選取所需的 Azure 訂用帳戶。
   資源群組 | **myresourcegroup** | 輸入新的資源群組，或您的訂用帳戶中現有的資源群組。
   伺服器名稱 | **mydemoserver** | 輸入唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 其必須包含 3 到 63 個字元。
   資料來源 |**None** | 選取 [無]  從頭建立新的伺服器。 只有在從現有伺服器的異地備份還原時，才需選取 [備份]。
   Location |您所需的位置 | 從清單中選取位置。
   版本 | 最新的主要版本| 使用最新的主要版本。 請參閱[所有支援的版本](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)。
   計算 + 儲存體 | 使用預設值| 預設定價層是 **一般用途** 搭配 **4 個虛擬核心** 以及 **100 GB** 的儲存體。 備份保留期設定為 **7 天**，且具備 **異地備援** 備份選項。<br/>檢閱 [[定價](https://azure.microsoft.com/pricing/details/mysql/)] 頁面，並視需要更新預設值。
   系統管理員使用者名稱 | **mydemoadmin** | 輸入您的伺服器管理使用者名稱。 您不可使用 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public** 作為管理使用者名稱。
   密碼 | 密碼 | 伺服器管理使用者的新密碼。 密碼的長度必須為 8 到 128 個字元，且包含大寫或小寫字母、數字和非英數字元 (!、$、#、% 等等) 的組合。
  

   > [!NOTE]
   > 如果您的工作負載只需要輕量計算和 I/O，請考慮使用基本定價層。 請注意，在基本定價層中建立的伺服器後續無法調整為「一般用途」或「記憶體最佳化」。

4. 選取 [檢閱+建立] 以佈建伺服器。

5. 等候入口網站頁面顯示 [您的部署已完成] 訊息。 選取 [前往資源]，移至新建立的伺服器頁面：

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="顯示「您的部署已完成」訊息的螢幕擷取畫面。":::

[有問題嗎？請告訴我們。](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>設定伺服器層級防火牆規則

根據預設，新的伺服器會受到防火牆保護。 若要連線，您必須完成下列步驟以提供 IP 的存取權：

1. 從伺服器資源的左窗格，移至 [連線安全性]。 如果您不知道如何尋找資源，請參閱[如何開啟資源](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="顯示 [連線安全性 > 防火牆規則] 頁面的螢幕擷取畫面。":::

2. 選取 [新增目前的用戶端 IP 位址]，然後選取 [儲存]。

   > [!NOTE]
   > 若要避免發生連線問題，請檢查您的網路是否允許連接埠 3306 的輸出流量，以供適用於 MySQL 的 Azure 資料庫使用。 

您可以新增更多 IP 或提供 IP 範圍，以從這些 IP 連線至您的伺服器。 如需詳細資訊，請參閱[如何在適用於 MySQL 的 Azure 資料庫上管理防火牆規則](./concepts-firewall-rules.md)。


[有問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>使用 mysql.exe 連線至伺服器
您可以從本機環境使用 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 連線至伺服器。 在本快速入門中，我們將在 [Azure Cloud Shell](../cloud-shell/overview.md) 中執行 mysql.exe，以連線至伺服器。


1. 選取工具列上的第一個按鈕，在入口網站中開啟 Azure Cloud Shell，如下列螢幕擷取畫面所示。 請在 [概觀] 區段中記下新伺服器的伺服器名稱、伺服器管理員名稱和訂用帳戶，如螢幕擷取畫面所示。

    > [!NOTE]
    > 如果您是第一次開啟 Cloud Shell，系統將會提示您建立資源群組和儲存體帳戶。 此步驟只需執行一次。 系統會為所有工作階段連結此項目。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="在 Azure 入口網站中顯示 Cloud Shell 的螢幕擷取畫面。":::
2. 在 Azure Cloud Shell 終端機內執行下列命令。 請將此處顯示的值取代為您的實際伺服器名稱和管理使用者名稱。 對於適用於 MySQL 的 Azure 資料庫，您必須將 `@\<servername>` 新增至管理使用者名稱，如下所示： 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      以下是在 Cloud Shell 終端機中呈現的樣貌：

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
3. 在相同的 Azure Cloud Shell 終端機中，建立名為 `guest` 的資料庫：
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. 切換至 `guest` 資料庫：
      ```
      mysql> USE guest;
      Database changed
      ```
5. 輸入 `quit`，然後選取 **Enter** 以結束 mysql。

[有問題嗎？請告訴我們。](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>清除資源
您此時已在資源群組中建立適用於 MySQL 的 Azure 資料庫伺服器。  如果您預期後續不需要這些資源，您可以刪除資源群組以刪除資源，或直接刪除 MySQL 伺服器。 若要移除資源群組，請完成下列步驟：
1. 在 Azure 入口網站中，搜尋並選取 [資源群組]。
2. 在資源群組清單中，選取資源群組的名稱。
3. 在資源群組的 [概觀] 頁面中，選取 [刪除資源群組]。
4. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

若要刪除伺服器，您可以在伺服器的 [概觀] 頁面上選取 [刪除]，如下所示：
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="顯示 [伺服器概觀] 頁面上的 [刪除] 按鈕的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上建立 PHP 應用程式](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[使用 MySQL 在 Linux 上建置 PHP 應用程式](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[找不到您要尋找的內容嗎？請告訴我們。](https://aka.ms/mysql-doc-feedback)
