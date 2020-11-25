---
title: 快速入門：建立適用於 MySQL 的 Azure 資料庫彈性伺服器 - Azure 入口網站
description: 本文將逐步引導您使用 Azure 入口網站在數分鐘內建立適用於 MySQL 的 Azure 資料庫彈性伺服器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566753"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>快速入門：使用 Azure 入口網站建立適用於 MySQL 的 Azure 資料庫彈性伺服器

適用於 MySQL 的 Azure 資料庫彈性伺服器是一個受管理的服務，可用來在雲端執行、管理及調整高可用性 MySQL 伺服器。 本快速入門說明如何使用 Azure 入口網站建立彈性伺服器。

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>建立適用於 MySQL 的 Azure 資料庫彈性伺服器

您可使用一組已定義的[計算和儲存體資源](./concepts-compute-storage.md)來建立彈性伺服器。 您可在 [Azure 資源群組](../../azure-resource-manager/management/overview.md)內建立伺服器。

請完成下列步驟以建立彈性伺服器：

1. 在入口網站中，搜尋並選取 [適用於 MySQL 的 Azure 資料庫伺服器]：
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="顯示搜尋適用於 MySQL 的 Azure 資料庫伺服器時的螢幕擷取畫面。":::

2. 選取 [新增]  。 

3. 在 [選取適用於 MySQL 的 Azure 資料庫部署選項] 頁面上，選取 [彈性伺服器] 作為部署選項：
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="顯示 [彈性伺服器] 選項的螢幕擷取畫面。":::    

4. 在 [基本] 索引標籤上，輸入下列資訊： 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="顯示 [彈性伺服器] 頁面的 [基本] 索引標籤的螢幕擷取畫面。"::: 
                                    
    |**設定**|**建議的值**|**說明**|
    |---|---|---|
    訂用帳戶|您的訂用帳戶名稱|您要用於伺服器的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇要對資源計費的訂用帳戶。|
    資源群組|**myresourcegroup**| 新的資源群組名稱，或您訂用帳戶中現有的資源群組名稱。|
    伺服器名稱 |**mydemoserver**|可識別您彈性伺服器的唯一名稱。 網域名稱 `mysql.database.azure.com` 會附加至您提供的伺服器名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 它必須包含 3 到 63 個字元。|
    系統管理員使用者名稱 |**mydemouser**| 您連線至伺服器時所要使用的登入帳戶。 管理使用者名稱不可為 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public**。|
    密碼 |您的密碼| 伺服器管理帳戶的新密碼。 其必須包含 8 到 128 個字元。 其中也必須包含下列三種類別的字元：英文大寫字母、英文小寫字母、數字 (0 到 9) 及非英數字元 (!、$、#、% 等等)。|
    區域|最接近使用者的區域| 最靠近您的使用者的位置。|
    版本|**5.7**| MySQL 主要版本。|
    計算 + 儲存體 | **高載**、**Standard_B1ms**、**10 GiB**、**7 天** | 新伺服器的計算、儲存體和備份組態。 選取 [設定伺服器]  。 **高載**、**Standard_B1ms**、**10 GiB** 和 **7 天** 是 **計算層**、**計算大小**、**儲存體大小** 和備份 **保留期間** 的預設值。 您可以讓這些值保持原狀或加以調整。 若要儲存計算和儲存體選取項目，請選取 [儲存] 以繼續設定。 下列螢幕擷取畫面顯示計算和儲存體選項。|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="顯示計算和儲存體選項的螢幕擷取畫面。":::

5. 設定網路選項。

    在 [網路] 索引標籤上，您可以選擇伺服器的連線方式。 適用於 MySQL 的 Azure 資料庫彈性伺服器提供兩種連線至伺服器的方式： 
   - 公用存取 (允許的 IP 位址)
   - 私人存取 (VNet 整合) 
   
   當您使用公用存取時，您的伺服器存取權僅限於您新增至防火牆規則的允許 IP 位址。 此方法會防止外部應用程式和工具連線至伺服器及伺服器上的任何資料庫，除非您建立規則以針對特定 IP 位址或範圍開啟防火牆。 當您使用私人存取 (VNet 整合) 時，您的伺服器存取權僅限於您的虛擬網路。 在本快速入門中，您將了解如何啟用公用存取以連線至伺服器。 [在概念文章中深入了解各種連線方法。](./concepts-networking.md)

    > [!NOTE]
    > 建立伺服器之後，您就無法變更連線方法。 例如，如果您在建立伺服器時選取了 [公用存取 (允許的 IP 位址)]，則在建立伺服器之後，就無法變更為 [私人存取 (VNet 整合)]。 強烈建議您建立具有私人存取權的伺服器，以利透過 VNet 整合來保護您的伺服器存取。 [在概念文章中深入了解私人存取。](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="顯示 [網路] 索引標籤的螢幕擷取畫面。":::  

6. 選取 [檢閱 + 建立]，檢閱彈性伺服器組態。

7. 選取 [建立] 以佈建伺服器。 佈建這可能需要數分鐘的時間。

8. 在工具列上選取 [通知] (鈴鐺按鈕) 以監視部署程序。 完成部署後，您可以選取 [釘選到儀表板]，在 Azure 入口網站儀表板上建立彈性伺服器的圖格。 此圖格是伺服器 [概觀] 頁面的捷徑。 當您選取 [前往資源] 時，伺服器的 [概觀] 頁面隨即開啟。

根據預設，您的伺服器底下會建立下列資料庫：information_schema、mysql、performance_schema 和 sys。

> [!NOTE]
> 若要避免發生連線問題，請檢查您的網路是否允許連接埠 3306 的輸出流量，以供適用於 MySQL 的 Azure 資料庫彈性伺服器使用。  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>使用 mysql.exe 連線至伺服器

如果您使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的虛擬網路中的資源連線至您的伺服器。 您可以建立虛擬機器，並將其新增至使用彈性伺服器建立的虛擬網路。

如果您使用公用存取 (允許的 IP 位址) 建立彈性伺服器，您可以將本機 IP 位址新增至伺服器上的防火牆規則清單。

您可以從本機環境使用 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 連線至伺服器。 

如果您使用 mysql，請使用下列命令進行連線。 在此命令中，請使用您的伺服器名稱、使用者名稱和密碼。 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>清除資源
現在，您已在資源群組中建立適用於 MySQL 的 Azure 資料庫彈性伺服器。 如果您預期後續不需要這些資源，您可以刪除資源群組以刪除資源，或直接刪除 MySQL 伺服器。 若要移除資源群組，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取 [資源群組]。
1. 在資源群組清單中，選取資源群組的名稱。
1. 在資源群組的 [概觀] 頁面中，選取 [刪除資源群組]。
1. 在確認對話方塊凹輸入您的資源群組名稱，然後選取 [刪除]。

若要刪除伺服器，您可以在伺服器的 [概觀] 頁面上選取 [刪除]，如下所示：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="顯示如何刪除伺服器的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 MySQL 建置 PHP (Laravel) Web 應用程式](tutorial-php-database-app.md)