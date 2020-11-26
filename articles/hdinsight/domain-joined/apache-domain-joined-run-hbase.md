---
title: Apache HBase 和企業安全性套件 - Azure HDInsight
description: 教學課程 - 了解如何使用企業安全性套件在 Azure HDInsight 中設定 HBase 的 Apache Ranger 原則。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 5747de399e7ae0cfe99ba013f8da376be0ba1b2a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993630"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>教學課程：使用企業安全性套件在 HDInsight 中設定 Apache HBase 原則

了解如何對企業安全性套件 (ESP) Apache HBase 叢集設定 Apache Ranger 原則。 ESP 叢集連線到網域，讓使用者使用網域認證進行驗證。 在此教學課程中，您會建立兩個 Ranger 原則來限制 HBase 資料表中不同資料行系列的存取權。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立網域使用者
> * 建立 Ranger 原則
> * 在 HBase 叢集中建立資料表
> * 測試 Ranger 原則

## <a name="before-you-begin"></a>開始之前

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

* 登入 [Azure 入口網站](https://portal.azure.com/)。

* 使用企業安全性套件建立 [HDInsight HBase 叢集](apache-domain-joined-configure-using-azure-adds.md)。

## <a name="connect-to-apache-ranger-admin-ui"></a>連線到 Apache Ranger 系統管理 UI

1. 從瀏覽器中，使用 URL `https://<ClusterName>.azurehdinsight.net/Ranger/` 連線到 Ranger 管理員使用者介面。 請記得將 `<ClusterName>` 變更為 HBase 叢集的名稱。

    > [!NOTE]  
    > Ranger 認證與 Hadoop 叢集認證並非相同。 若要避免瀏覽器使用快取的 Hadoop 認證，請使用新的 InPrivate 瀏覽器視窗連線至 Ranger 管理員 UI。

2. 使用您的 Azure Active Directory (AD) 管理員認證登入。 Azure AD 管理員認證與 HDInsight 叢集認證或 Linux HDInsight 節點 SSH 認證並非相同。

## <a name="create-domain-users"></a>建立網域使用者

請參閱 [使用企業安全性套件建立 HDInsight 叢集](./apache-domain-joined-configure-using-azure-adds.md)，以了解如何建立 **sales_user1** 和 **marketing_user1** 網域使用者。 在生產情節中，網域使用者來自 Active Directory 租用戶。

## <a name="create-hbase-tables-and-import-sample-data"></a>建立 HBase 資料表並匯入範例資料

您可以使用 SSH 來連線到 HBase 叢集，然後使用 [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) 來建立 HBase 資料表、插入及查詢資料。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

### <a name="to-use-the-hbase-shell"></a>使用 HBase 殼層

1. 從 SSH，執行下列 HBase 命令：
   
    ```bash
    hbase shell
    ```

2. 使用兩個資料行系列建立 HBase 資料表 `Customers`：`Name` 和 `Contact`。

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. 插入一些資料：
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. 檢視資料表的內容：
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![HDInsight Hadoop HBase 殼層輸出](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>建立 Ranger 原則

為 **sales_user1** 和 **marketing_user1** 建立 Ranger 原則。

1. 開啟 **Ranger 管理員 UI**。 按一下 [HBase] 底下的 [\<ClusterName>_hbase]。

   ![HDInsight Apache Ranger 系統管理員 UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. **原則清單** 畫面會顯示為此叢集建立的所有 Ranger 原則。 可能會列出其中一個預先設定的原則。 按一下 [新增原則]  。

    ![Apache Ranger HBase 原則清單](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. 在 [建立原則]  畫面上輸入下列值：

   |**設定**  |**建議的值**  |
   |---------|---------|
   |原則名稱  |  sales_customers_name_contact   |
   |HBase 資料表   |  客戶 |
   |HBase 資料行系列   |  名稱、連絡資訊 |
   |HBase 資料行   |  * |
   |選取群組  | |
   |選取使用者  | sales_user1 |
   |權限  | 讀取 |

   主題名稱可以包含下列萬用字元：

   * `*` 表示出現零次以上的字元。
   * `?` 表示單一字元。

   ![Apache Ranger 原則建立銷售](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >如果 [選取使用者]  未自動填入網域使用者，請稍候 Ranger 與 Azure AD 同步處理。

4. 按一下 [新增]  以儲存規則。

5. 按一下 [新增原則]  ，然後輸入下列值︰

   |**設定**  |**建議的值**  |
   |---------|---------|
   |原則名稱  |  marketing_customers_contact   |
   |HBase 資料表   |  客戶 |
   |HBase 資料行系列   |  連絡人 |
   |HBase 資料行   |  * |
   |選取群組  | |
   |選取使用者  | marketing_user1 |
   |權限  | 讀取 |

   ![Apache Ranger 原則建立行銷](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. 按一下 [新增]  以儲存規則。

## <a name="test-the-ranger-policies"></a>測試 Ranger 原則

根據所設定的 Ranger 原則，**sales_user1** 可以檢視 `Name` 和 `Contact` 資料行系列中所有資料行資料。 **Marketing_user1** 只能檢視 `Contact` 資料行系列中的資料。

### <a name="access-data-as-sales_user1"></a>以 sales_user1 的身分存取資料

1. 對於叢集開啟新的 SSH 連線。 使用下列命令來登入叢集：

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. 使用 kinit 命令來變更為我們所需的使用者內容。

   ```bash
   kinit sales_user1
   ```

2. 開啟 HBase 殼層，並掃描資料表 `Customers`。

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. 請注意，銷售使用者可以檢視 `Customers` 資料表的所有資料行，包括 `Name` 資料行系列中的兩個資料行，以及 `Contact` 資料行系列中的五個資料行。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>以 marketing_user1 的身分存取資料

1. 對於叢集開啟新的 SSH 連線。 下列命令可用來以 **marketing_user1** 身分登入：

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. 使用 kinit 命令來變更為我們所需的使用者內容

   ```bash
   kinit marketing_user1
   ```

1. 開啟 HBase 殼層，並掃描資料表 `Customers`：

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. 請注意，行銷使用者只能檢視 `Contact` 資料行系列的五個資料行。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. 從 Ranger UI 檢視稽核存取事件。

   ![HDInsight Ranger UI 原則稽核](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟來刪除所建立的 HBase 叢集：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頂端的 [搜尋]  方塊中，輸入 **HDInsight**。 
1. 在 [服務]  底下，選取 [HDInsight 叢集]  。
1. 從出現的 HDInsight 叢集清單中，在您為本教學課程建立的叢集旁按一下 [...]  。 
1. 按一下 **[刪除]** 。 按一下 [是]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用 Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)