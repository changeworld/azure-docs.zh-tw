---
title: 阿帕奇遊俠中的 Apache 蜂巢策略 - Azure HDInsight
description: 了解如何在具有企業安全性套件的 Azure HDInsight 服務中設定 Hive 的 Apache Ranger 原則。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196921"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>在具有企業安全性套件的 HDInsight 中設定 Apache Hive 原則

了解如何針對 Apache Hive 設定 Apache Ranger 原則。 在本文中，您會建立兩個 Ranger 原則來限制 hivesampletable 的存取權。 HDInsight 叢集隨附 hivesampletable。 配置策略後，使用 Excel 和 ODBC 驅動程式連接到 HDInsight 中的 Hive 表。

## <a name="prerequisites"></a>Prerequisites

* 具有企業安全性套件的 HDInsight 叢集。 請參閱[設定具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure.md)。
* 安裝 Office 2016、Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的工作站。

## <a name="connect-to-apache-ranger-admin-ui"></a>連線到 Apache Ranger 系統管理 UI
**到 Ranger 系統管理 UI**

1. 從瀏覽器導航到遊俠管理員 UI，其中`https://CLUSTERNAME.azurehdinsight.net/Ranger/`CLUSTERNAME 是群集的名稱。

   > [!NOTE]  
   > Ranger 會使用與 Apache Hadoop 叢集不同的認證。 若要避免瀏覽器使用快取的 Hadoop 認證，請使用新的 InPrivate 瀏覽器視窗連線至 Ranger 系統管理 UI。

2. 使用叢集系統管理員網域使用者名稱和密碼進行登入：

    ![HDInsight ESP Ranger 首頁](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    目前，Ranger 僅適用於 Yarn 和 Hive。

## <a name="create-domain-users"></a>建立網域使用者

如需如何建立 hiveruser1 與 hiveuser2 的相關資訊，請參閱[建立具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。 在本文中，您可以使用兩個使用者帳戶。

## <a name="create-ranger-policies"></a>建立 Ranger 原則

在這一節中，您會建立兩個 Ranger 原則以供存取 hivesampletable。 您會提供不同資料行集的選取權限。 兩個使用者都是使用[建立具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)中的指示建立。 在下一節中，您將在 Excel 中測試兩個策略。

**建立 Ranger 原則**

1. 開啟 Ranger 系統管理 UI。 請參閱連接到 Apache Ranger 系統管理 UI。
2. 選擇**CLUSTERNAME_Hive，** 在**蜂巢**下。 您會看到兩個預先設定的原則。
3. 選擇 **"添加新策略**"，然後輸入以下值：

    |屬性 |值 |
    |---|---|
    |原則名稱|讀-hive樣表-所有|
    |蜂巢資料庫|default|
    |資料表|蜂巢表|
    |蜂巢柱|*|
    |選取使用者|蜂巢1|
    |權限|select|

    ![HDInsight ESP 遊俠蜂巢策略配置](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > 如果 [選取使用者] 中未填入網域使用者，請稍等一下讓 Ranger 與 AAD 同步處理。

4. 選取 [新增]**** 以儲存規則。

5. 重複最後兩個步驟，使用下列屬性建立另一個原則︰

    |屬性 |值 |
    |---|---|
    |原則名稱|讀-hive樣表-設備製作|
    |蜂巢資料庫|default|
    |資料表|蜂巢表|
    |蜂巢列|用戶端 id，設備製造|
    |選取使用者|蜂巢2|
    |權限|select|

## <a name="create-hive-odbc-data-source"></a>建立 Hive ODBC 資料來源

在[建立 Hive ODBC 資料來源](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)中可找到相關指示。  

 | 屬性  |描述 |
 | --- | --- |
 | 資料來源名稱 | 為資料來源指定名稱 |
 | Host | 輸入CLUSTERNAME.azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net |
 | 連接埠 | 使用 **443** (此連接埠已從 563 變更為 443)。 |
 | 資料庫 | 使用**預設**。 |
 | Hive 伺服器類型 | 選取 [Hive Server 2]**** |
 | 機制 | 選取 [Azure HDInsight 服務]**** |
 | HTTP 路徑 | 保留為空白。 |
 | 使用者名稱 | 輸入 hiveuser1@contoso158.onmicrosoft.com。 如果功能變數名稱不同，則更新功能變數名稱。 |
 | 密碼 | 輸入 hiveuser1 的密碼。 |

請務必先按一下 [測試]****，再儲存資料來源。

## <a name="import-data-into-excel-from-hdinsight"></a>從 HDInsight 將資料匯入 Excel 中

在最後一節中，您配置了兩個策略。  hiveuser1 具有所有資料行的選取權限，而 hiveuser2 具有兩個資料行的選取權限。 本節中，您可以模擬兩位使用者將資料匯入 Excel 中。

1. 在 Excel 中開啟新的或現有的活頁簿。

1. 從 [資料]**** 索引標籤，瀏覽至 [取得資料]**** > [從其他來源]**** > [從 ODBC]**** 以啟動 [從 ODBC]**** 視窗。

    ![Open data connection wizard](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. 從下拉清單中，選擇您在最後一節中創建的資料來源名稱，然後選擇 **"確定**"。

1. 對於首次使用，將打開**一個 ODBC 驅動程式**對話方塊。 從左側功能表中選擇 **"視窗**"。 然後選擇 **"連接"** 以打開**導航器**視窗。

1. 等待 [選取資料庫及資料表] **** 對話方塊開啟。 這可能需要幾秒鐘的時間。

1. 選擇**蜂巢，** 然後選擇 **"下一步**"。

1. 選取 [完成]****。

1. 在 [匯入資料] **** 對話方塊中，您可以變更或指定查詢。 為此，請選擇**屬性**。 這可能需要幾秒鐘的時間。

1. 選擇"**定義"** 選項卡。命令文本為：

       SELECT * FROM "HIVE"."default"."hivesampletable"

   您定義的 Ranger 原則，hiveuser1 會有所有資料行的選取權限。  因此，此查詢適用于 hiveuser1 的憑據，但此查詢不能與 hiveuser2 的憑據一起使用。

1. 選擇 **"確定"** 可關閉連接屬性對話方塊。

1. 選擇 **"確定"** 以關閉 **"導入資料"** 對話方塊。  

1. 重新輸入 hiveuser1 的密碼，然後按一下 [確定]****。 經過數秒後，資料即會匯入至 Excel。 完成後，您將看到 11 列資料。

測試您在上一節中建立的第二個原則 (read-hivesampletable-devicemake)

1. 在 Excel 中新增工作表。
2. 依照上一個程序匯入資料。  您會進行的唯一變更是使用 hiveuser2 的認證，而非使用 hiveuser1 的認證。 這會失敗，因為 hiveuser2 只有查看兩個資料行的權限。 您會收到下列錯誤︰

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. 依照相同程序匯入資料。 這次使用 hiveuser2 的認證，並且修改 select 陳述式，從︰

        SELECT * FROM "HIVE"."default"."hivesampletable"

    至：

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    完成後，您將看到兩列導入的資料。

## <a name="next-steps"></a>後續步驟

* 如需設定具有企業安全性套件的 HDInsight 叢集，請參閱[設定具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure.md)。
* 如需管理具有 ESP 的 HDInsight 叢集，請參閱[管理具有 ESP 的 HDInsight 叢集](apache-domain-joined-manage.md)。
* 如需在具有 ESP 的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
* 如需使用 Hive JDBC 連接 Hive，請參閱 [使用 Hive JDBC 驅動程式連接到 Azure HDInsight 上的 Apache Hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* 如需使用 Hive ODBC 將 Excel 連接到 Hadoop，請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* 如需使用 Power Query 將 Excel 連接到 Apache Hadoop，請參閱[使用 Power Query 將 Excel 連接到 Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)
