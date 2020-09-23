---
title: 快速入門：建立適用於 MySQL 的 Azure DB 彈性伺服器 - Azure 入口網站
description: 本文逐步引導您使用 Azure 入口網站，在數分鐘內快速建立適用於 MySQL 的 Azure 資料庫彈性伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 4d4d65f9ad04ca6bf99375647684a75e8662bb4d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943872"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>快速入門：使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫彈性伺服器

適用於 MySQL 的 Azure 資料庫彈性伺服器是一個受控服務，您可用來在雲端執行、管理及調整高可用性 MySQL 伺服器。 本快速入門會說明使用 Azure 入口網站建立彈性伺服器的數種方法。

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
開啟 Web 瀏覽器，然後移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>建立適用於 MySQL 的 Azure 資料庫彈性伺服器

您可使用一組已定義的[計算和儲存體資源](./concepts-compute-storage.md)來建立彈性伺服器。 您可在 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)內建立伺服器。

請遵循這些步驟建立彈性伺服器：

1. 在入口網站的左上角，選取 [建立資源]  (+)。

2. 選取 [資料庫]   > [Azure Database for MySQL]  。 您也可以在搜尋方塊中輸入 **MySQL** 以尋找此服務。

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="適用於 MySQL 的 Azure 資料庫 選項":::

3. 選取 [彈性伺服器] 作為部署選項。
     
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="挑選部署選項 ":::    

4. 在 [基本資料]  表單中填寫下列資訊： 

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="建立伺服器表單"::: 
                                    
    |**設定**|**建議的值**|**說明**|
    |---|---|---|
    訂用帳戶|您的訂用帳戶名稱|您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的訂用帳戶。|
    資源群組|*myresourcegroup*| 新的資源群組名稱，或您訂用帳戶中現有的資源群組名稱。|
    伺服器名稱 |*mydemoserver*|可識別您彈性伺服器的唯一名稱。 網域名稱 mysql.database.azure.com 已附加至您提供的伺服器名稱。 伺服器只能包含小寫字母、數字及連字號 (-) 字元。 它必須包含至少 3 到 63 個字元。|
    系統管理員使用者名稱 |*mydemouser*| 連線至伺服器時所要使用之自己的登入帳戶。 管理員登入名稱不能是 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public**。|
    密碼 |您的密碼| 伺服器管理帳戶的新密碼。 其必須包含 8 到 128 個字元。 您的密碼必須包含下列類別中三種類別的字元：英文大寫字母、英文小寫字母、數字 (0 到 9) 及非英數字元 (!、$、#、% 等等)。|
    區域|最接近使用者的區域| 最靠近您使用者的位置。|
    版本|5.7| MySQL 主要版本。|
    計算 + 儲存體 | **高載**、**Standard_B1ms**、**10 GiB**、**7 天** | 新伺服器的計算、儲存體和備份組態。 選取 [設定伺服器]  。 高載、Standard_B1ms、10 GiB 和 7 天是**計算層級**、**計算大小**、**儲存體**和**備份保留期間**的預設值。 您可以讓這些滑桿保持原狀或加以調整。 若要儲存此計算和儲存體選取項目，請選取 [儲存] 以繼續設定。 下列螢幕擷取畫面顯示計算和儲存體選項。|
    
    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="計算 + 儲存體":::

5. 設定網路功能選項

    在網路功能索引標籤上，您可以選擇伺服器的連線方式。 適用於 MySQL 的 Azure 資料庫彈性伺服器提供公用存取 (允許的 IP 位址) 和私人存取 (VNet 整合) 這兩種方式連線到伺服器。 使用公用存取 (允許的 IP 位址)，您的伺服器存取權僅限於已新增至防火牆規則的允許 IP 位址。 此選項會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非您建立規則以針對特定 IP 位址或範圍開啟防火牆。 使用私人存取 (VNet 整合)，您的伺服器存取權僅限於您的虛擬網路。 在本快速入門中，我們會示範如何啟用公用存取以連線到伺服器。 在[概念文章](./concepts-networking.md)中深入了解各種連線方法。

    > [!NOTE]
    > 建立伺服器後，就無法變更連線方法。 例如，如果您在建立期間選取了「公用存取 (允許的 IP 位址)」，則在建立之後，就無法變更為「私人存取 (VNet 整合)」。 強烈建議您建立具有私人存取權的伺服器，才能使用 VNet 整合安全地存取您的伺服器。 若要深入了解私人存取，請參閱[概念文章](./concepts-networking.md)。

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="設定網路功能":::  

    <!--:::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Select "Add current client IP address"":::-->

6. 選取 [檢閱 + 建立]，檢閱彈性伺服器組態。

7. 選取 [建立] 以佈建伺服器。 佈建這可能需要數分鐘的時間。

8. 在工具列上選取 [通知] \(鈴鐺圖示) 以監視部署程序。 完成部署後，您可以選取 [釘選到儀表板]，在 Azure 入口網站儀表板上建立此彈性伺服器的圖格，以作為伺服器 [概觀] 頁面的捷徑。 選取 [移至資源]**** 會開啟伺服器的 [概觀]**** 頁面。

根據預設，您的伺服器底下會建立下列資料庫：**information_schema**、**mysql**、**performance_schema** 和 **sys**。

> [!NOTE]
> 檢查您的網路是否允許連接埠 3306 的輸出流量，以供適用於 MySQL 的 Azure 資料庫彈性伺服器使用以避免連線問題。  

## <a name="connect-to-using-mysql-command-line-client"></a>使用 mysql 命令列用戶端連線

如果您使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的 VNet 中的資源連線到您的伺服器。 您可以建立虛擬機器，並新增至使用您彈性伺服器建立的 VNet。

如果您使用公用存取 (允許的 IP 位址)建立彈性伺服器，可以將本機 IP 位址新增至伺服器上的防火牆規則清單。

您可以從本機環境中選擇 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 來連線到伺服器。 

使用 mysql.exe，使用下列命令連線。 以實際的伺服器名稱和密碼取代這些值。 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>清除資源
您已成功在資源群組中建立適用於 MySQL 的 Azure 資料庫彈性伺服器。  如果您覺得未來不需要這些資源，可以刪除資源群組，或刪除 MySQL 伺服器。 若要移除資源群組，請依照這些步驟操作：

1. 在 Azure 入口網站中，搜尋並選取 [資源群組]。
1. 在 [資源群組] 清單中，選擇資源群組的名稱。
1. 在資源群組的 [概觀]頁面中，選取 [刪除資源群組]。
1. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

若要刪除伺服器，您可以在伺服器的 [概觀] 頁面上按一下 [刪除] 按鈕，如下所示：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="刪除資源":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 MySQL 建置 PHP (Laravel) Web 應用程式](tutorial-php-database-app.md)
