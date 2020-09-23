---
title: 匯入和匯出-適用於 MySQL 的 Azure 資料庫
description: 本文說明在適用於 MySQL 的 Azure 資料庫中，使用 MySQL Workbench 之類的工具匯入與匯出資料庫的常見方式。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/22/2020
ms.openlocfilehash: 6d0a29d8ef8123eafd6a1616a24003c1e36e6e59
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905932"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>使用匯入和匯出移轉您的 MySQL 資料庫
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
本文說明使用 MySQL Workbench 將資料匯入與匯出適用於 MySQL 伺服器的 Azure 資料庫的兩個常見方式。

## <a name="before-you-begin"></a>開始之前
若要逐步執行本作法指南，您需要︰
- 適用於 MySQL 伺服器的 Azure 資料庫，[使用 Azure 入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)。
- 可進行匯入/匯出的[Mysql 工作臺](https://dev.mysql.com/downloads/workbench/)或其他協力廠商 mysql 工具。

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>在適用於 MySQL 伺服器的 Azure 資料庫上建立資料庫
使用 MySQL 工作臺、Toad 或 Navicat 建立資料庫，在適用於 MySQL 的 Azure 資料庫伺服器上建立空的資料庫。 資料庫名稱可以與包含傾印資料的資料庫名稱相同，或者您可以建立名稱不同的資料庫。

若要連線，在適用於 MySQL 之 Azure 資料庫的 [概觀] 中找到連線資訊。

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="在 Azure 入口網站中尋找連線資訊":::

將連線資訊新增至 MySQL Workbench。

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL 工作臺連接字串":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>判斷何時使用匯入和匯出技術

> [!TIP]
> 在您想要傾印和還原整個資料庫的案例中，您應該改用傾印 [和還原](concepts-migrate-dump-restore.md) 方法。

在下列案例中，使用 MySQL 工具將資料庫匯入與匯出 Azure MySQL 資料庫。

- 當您需要選擇性地選擇從現有 MySQL 資料庫匯入至 Azure MySQL 資料庫的一些資料表時，最好使用匯入和匯出技術。  如此一來，您可以從移轉省略任何不必要的資料表，以節省時間和資源。 例如，搭配使用 `--include-tables` 或 `--exclude-tables` 參數與 [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) 以及搭配使用 `--tables` 參數與 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables)。
- 移動資料表以外的資料庫物件時，明確建立這些物件。 包含限制式 (主索引鍵、外部索引鍵、索引)、檢視、函式、程序、觸發程序和其他您想要移轉的資料庫物件。
- 當您從 MySQL 資料庫以外的外部資料來源移轉資料時，建立一般檔案，並且使用 [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html) 來匯入它們。

> [!Important]
> 單一伺服器和彈性的伺服器 **僅支援 InnoDB 儲存引擎**。 將資料載入至適用於 MySQL 的 Azure 資料庫時，請確定資料庫中的所有資料表都使用 InnoDB 儲存引擎。
> 如果您的源資料庫使用其他儲存引擎，請在遷移資料庫之前轉換成 InnoDB 引擎。 例如，如果您有使用 MyISAM 引擎的 WordPress 或 Web 應用程式，先將資料移轉至 InnoDB 資料表來轉換資料表。 使用子句 `ENGINE=INNODB` 以設定引擎來建立資料表，然後在移轉之前將資料傳送到相容的資料表。

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>匯入和匯出的效能建議
-   載入資料之前先建立叢集索引和主索引鍵。 以主索引鍵的順序載入資料。
-   延遲建立次要索引，直到載入資料。 載入之後建立所有次要索引。
-   載入之前停用外部索引鍵限制式。 停用外部索引鍵檢查會提供顯著的效能提升。 啟用限制式並且確認載入之後的資料，以確保參考完整性。
-   平行載入資料。 避免會導致您達到資源限制的太多平行處理原則，以及使用 Azure 入口網站中可用的計量監視資源。
-   適當時使用資料分割資料表。

## <a name="import-and-export-by-using-mysql-workbench"></a>使用 MySQL Workbench 匯入和匯出
有兩種方式可在 MySQL Workbench 中匯出和匯入資料。 每一種適用於不同用途。

> [!NOTE]
> 如果您要將連線新增至 mysql 的單一或彈性伺服器 (預覽版) 在 MySQL 工作臺上，請確定：
> - 若為 MySQL 單一伺服器，使用者名稱必須採用下列格式： ' username@servername '
> - 針對 MySQL 彈性伺服器，只要使用 ' ' 來連線，就可以使用 ' username ' username@servername ，連接將會失敗。

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>物件瀏覽器快顯功能表的資料表資料匯出與匯入精靈
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="物件瀏覽器之快顯功能表上的 MySQL Workbench 精靈":::

資料表資料的精靈支援使用 CSV 和 JSON 檔案的匯入和匯出作業。 它們包含數個設定選項，例如分隔符號、資料行選取和編碼選取項目。 您可以對本機或遠端連線的 MySQL 伺服器執行每個精靈。 匯入動作包括資料表、資料行和類型對應。

您可以從物件瀏覽器的快顯功能表以滑鼠右鍵按一下資料表，來存取這些精靈。 然後選擇 [資料表資料匯出精靈]**** 或 [資料表資料匯入精靈]****。

#### <a name="table-data-export-wizard"></a>資料表資料匯出精靈
下列範例會將資料表匯出至 CSV 檔案：
1. 以滑鼠右鍵按一下要匯出之資料庫的資料表。
2. 選取 [資料表資料匯出精靈]****。 選取要匯出的資料行、資料列位移 (如果有的話) 和計數 (如果有的話)。
3. 在 [選取要匯出的資料]**** 分頁上，按 [下一步]****。 選取檔案路徑、CSV 或 JSON 檔案類型。 同時選取行分隔符號、字串封入方法和欄位分隔符號。
4. 在 [選取輸出檔案位置]**** 分頁上，按 [下一步]****。
5. 在 [匯出資料]**** 分頁上，按 [下一步]****。

#### <a name="table-data-import-wizard"></a>資料表資料匯入精靈
下列範例會從 CSV 檔案匯入資料表：
1. 以滑鼠右鍵按一下要匯入之資料庫的資料表。
2. 瀏覽並選取要匯入的 CSV 檔案，然後按 [下一步]****。
3. 選取目的地資料表 (新的或現有的)、選取或取消選取 [匯入前截斷資料表]**** 核取方塊。 按 [下一步]  。
4. 選取編碼方式和要匯入的資料行，然後按 [下一步]****。
5. 在 [匯入資料]**** 分頁上，按 [下一步]****。 精靈會據以匯入資料。

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>從導覽器窗格存取 SQL 資料匯出和匯入精靈
使用精靈，匯出或匯入從 MySQL Workbench 或 mysqldump 命令產生的 SQL。 從 [導覽器]**** 窗格或從主功能表選取 [伺服器]****，來存取這些精靈。 然後選取 [資料匯出]**** 或 [資料匯入]****。

#### <a name="data-export"></a>資料匯出
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="使用導覽器窗格的 MySQL Workbench 資料匯出":::

您可以使用 [資料匯出]**** 索引標籤，匯出您的 MySQL 資料。
1. 選取每個您想要匯出的結構描述、選擇性地從每個結構描述中選取特定的結構描述物件/資料表，然後產生匯出。 設定選項包含匯出到專案資料夾或自封式 SQL 檔案、傾印儲存的常式和事件，或略過資料表資料。

   或者，在 SQL 編輯器中，使用 [匯出結果集]****，將特定的結果集匯出為另一種格式，例如 CSV、JSON、HTML 和 XML。
3. 選取要匯出的資料庫物件，並設定相關選項。
4. 按一下 [重新整理]**** 以載入目前的物件。
5. 選擇性開啟 [進階選項]**** 索引標籤，以調整匯出作業。 例如，新增資料表鎖定、使用 replace 而不是 insert 陳述式，以及使用反引號字元將識別項括起來。
6. 按一下 [開始匯出]**** 開始匯出程序。


#### <a name="data-import"></a>資料匯入
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="使用管理導覽器的 MySQL Workbench 資料匯入":::

您可以使用 [資料匯入]**** 索引標籤，匯入或還原來自資料匯出作業或是來自 mysqldump 命令的已匯出資料。
1. 選擇專案資料夾或自封式 SQL 檔案、選擇將匯入的結構描述，或選擇 [新增]**** 來定義新的結構描述。
2. 按一下 [開始匯入]**** 開始匯入程序。

## <a name="next-steps"></a>下一步
- 至於另一個移轉方法，請參閱[在適用於 MySQL 的 Azure 資料庫中使用傾印和還原來移轉 MySQL 資料庫](concepts-migrate-dump-restore.md)。
- 若要深入了解如何將資料庫移轉至適用於 MySQL 的 Azure 資料庫，請參閱[資料庫移轉指南](https://aka.ms/datamigration)。
