---
title: 管理企業安全包群集 - Azure HDInsight
description: 瞭解如何使用企業安全包管理 Azure HDInsight 群集。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435903"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>使用企業安全性套件管理 HDInsight 叢集

了解 HDInsight 企業安全性套件 (ESP) 中的使用者與角色，以及如何管理 ESP 叢集。

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>使用 VSCode 連結至已加入網域的叢集

您可以使用 Apache Ambari 受控使用者名稱來連結正常的叢集，也可以使用網域使用者名稱 (例如：`user1@contoso.com`) 來連結安全性 Apache Hadoop 叢集。

1. 開啟 [Visual Studio Code](https://code.visualstudio.com/)。 確保安裝了[火花&蜂巢工具](../hdinsight-for-vscode.md)擴展。

1. 按照連結視覺化工作室代碼[群集](../hdinsight-for-vscode.md#link-a-cluster)的步驟操作。

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>使用 IntelliJ 連結至已加入網域的叢集

您可以使用 Ambari 受控使用者名稱來連結正常的叢集，也可以使用網域使用者名稱 (例如：`user1@contoso.com`) 來連結安全性 hadoop 叢集。

1. 開啟 IntelliJ IDEA。 確保所有[先決條件](../spark/apache-spark-intellij-tool-plugin.md#prerequisites)都得到滿足。

1. 按照連結 IntelliJ[群集](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster)的步驟操作。

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>使用 Eclipse 連結至已加入網域的叢集

您可以使用 Ambari 受控使用者名稱來連結正常的叢集，也可以使用網域使用者名稱 (例如：`user1@contoso.com`) 來連結安全性 hadoop 叢集。

1. 開啟 Eclipse。 確保所有[先決條件](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites)都得到滿足。

1. 按照連結 Eclipse[群集](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster)的步驟操作。

## <a name="access-the-clusters-with-enterprise-security-package"></a>使用企業安全包訪問群集

企業安全性套件 (先前稱為 HDInsight Premium) 提供對叢集的多位使用者存取，其驗證是由 Active Directory 執行，並且由 Apache Ranger 和 Storage ACL (ADLS ACL) 授權。 授權會提供多位使用者間的安全界限，只允許特殊權限的使用者根據授權原則存取資料。

安全性和使用者隔離對於使用企業安全性套件的 HDInsight 叢集很重要。 為了符合這些需求，會封鎖使用企業安全性套件之叢集的 SSH 存取。 下表顯示每個叢集類型的建議存取方法：

|工作負載|狀況|存取方法|
|--------|--------|-------------|
|Apache Hadoop|Hive – 互動式作業/查詢  |<ul><li>[Beeline](#beeline)</li><li>[蜂巢視圖](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|互動式作業/查詢，PySpark 互動式|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin with Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[蜂巢視圖](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|批次案例 – Spark 提交，PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|互動式查詢 (LLAP)|互動式|<ul><li>[Beeline](#beeline)</li><li>[蜂巢視圖](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|任意|安裝自訂應用程式|<ul><li>[指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > 企業安全性套件不會安裝/支援 Jupyter。

從安全性觀點來說，使用標準 API 會有所幫助。 您還可以獲得以下好處：

- **管理** – 您可以使用標準 API (Livy、HS2 等等) 管理您的程式碼並自動化作業。
- **審核**– 使用 SSH，無法審核哪些使用者 SSH 會到群集。 當作業是透過標準端點建構時，作業會在使用者內容中執行，所以這不是問題。

### <a name="use-beeline"></a><a name="beeline"></a>使用 BeeLine

在您的機器上安裝 Beeline，並透過公用網際網路連線，使用下列參數：

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

若您有在本機上安裝 Beeline 並透過公用網際網路連線，請使用下列參數：

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

若要找出前端節點的完整網域名稱，請利用＜使用 Ambari REST API 管理 HDInsight＞文件中的資訊。

## <a name="users-of-hdinsight-clusters-with-esp"></a>有 ESP 的 HDInsight 叢集的使用者

非 ESP 的 HDInsight 叢集有兩個使用者帳戶，是在叢集建立期間建立的︰

- **Ambari 系統管理員**：此帳戶亦稱為「Hadoop 使用者」** 或「HTTP 使用者」**。 此帳戶可用於在 登錄 Ambari。 `https://CLUSTERNAME.azurehdinsight.net` 它還可用於在 Ambari 視圖上執行查詢、通過外部工具執行作業（例如，PowerShell、Templeton、Visual Studio），以及使用 Hive ODBC 驅動程式和 BI 工具（例如 Excel、Power BI 或 Tableau）進行身份驗證。

除了 Ambari 系統管理員之外，使用 ESP 的 HDInsight 叢集有三個新使用者。

- **Ranger 系統管理員**︰此帳戶是本機的 Apache Ranger 系統管理員帳戶。 它不是活動目錄域使用者。 此帳戶可以用來設定原則、將其他使用者設為系統管理員、或委派系統管理工作 (讓某些使用者可以管理原則)。 預設的使用者名稱是 admin**，密碼則是與 Ambari 系統管理員的密碼相同。 可以在 Ranger 的 [設定] 頁面中更新密碼。

- **叢集系統管理員網域使用者**︰此帳戶是被指定為 Hadoop 叢集系統管理員 (包括 Ambari 和 Ranger) 的 Active Directory 網域使用者。 您必須在叢集建立期間提供此使用者的認證。 此使用者有下列權限：
    - 在叢集建立期間，將機器加入網域，並將它們放入您指定的 OU 中。
    - 在叢集建立期間，於您指定的 OU 中建立服務主體。
    - 建立反向 DNS 項目。

    請注意，其他的 AD 使用者也有這些權限。

    群集中有一些終結點（例如，鄧普頓），它們不受 Ranger 的管理，因此不安全。 這些端點會針對所有使用者鎖定，只有叢集系統管理員網域使用者除外。

- **一般**︰在叢集建立期間，您可以提供多個 Active Directory 群組。 這些群組中的使用者都將同步至 Ranger 和 Ambari。 這些使用者是網域使用者，只有存取 Ranger 管理之端點 (例如Hiveserver2) 的權限。 所有 RBAC 原則和稽核皆不適用於這些使用者。

## <a name="roles-of-hdinsight-clusters-with-esp"></a>使用 ESP 之 HDInsight 叢集的角色

HDInsight 企業安全性套件有下列角色：

- 叢集系統管理員
- 叢集操作員
- 服務管理員
- 服務操作員
- 叢集使用者

**若要查看這些角色的權限**

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 從左側功能表中，選擇 **"角色**"。
3. 選擇藍色問號以查看許可權：

    ![ESP HDInsight 角色權限](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>開啟 Ambari 管理 UI

1. 導航到`https://CLUSTERNAME.azurehdinsight.net/`CLUSTERNAME 是群集名稱的位置。
1. 使用群集管理員功能變數名稱使用者名和密碼登錄 Ambari。
1. 從右上角選擇**管理員**下拉式功能表，然後選擇 **"管理 Ambari**"。

    ![ESP HDInsight 管理阿帕奇·安巴里](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI 看起來像這樣：

    ![ESP HDInsight 阿帕奇·安巴里管理 UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>列出從您的 Active Directory 同步處理的網域使用者

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 從左側功能表中，選擇 **"使用者**"。 您應該會看到從您的 Active Directory 同步至 HDInsight 叢集的所有使用者。

    ![ESP HDInsight Ambari 管理 UI 清單使用者](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>列出從您的 Active Directory 同步處理的網域群組

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 從左側功能表中，選擇 **"組**"。 您應該會看到從您的 Active Directory 同步至 HDInsight 叢集的所有群組。

    ![ESP HDInsight Ambari 管理 UI 清單群組](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>設定 Hive 檢視權限

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 從左側功能表中，選擇 **"視圖**"。
3. 選擇**HIVE**以顯示詳細資訊。

    ![ESP HDInsight Ambari 管理 UI Hive 檢視](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. 選擇**蜂巢視圖**連結以配置蜂巢視圖。
5. 向下捲動至 [權限]****。

    ![ESP HDInsight Ambari 管理 UI Hive 檢視設定權限](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. 選擇 **"添加使用者**"或 **"添加組**"，然後指定可以使用 Hive 視圖的使用者或組。

## <a name="configure-users-for-the-roles"></a>設定角色的使用者

 若要查看角色及其權限的清單，請參閱使用 ESP 之 HDInsight 叢集的角色。

1. 開啟 Ambari 管理 UI。  請參閱[開啟 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 從左側功能表中，選擇 **"角色**"。
3. 選擇 **"添加使用者**"或 **"添加組**"以將使用者和組分配給不同角色。

## <a name="next-steps"></a>後續步驟

- 如需設定具有企業安全性套件的 HDInsight 叢集，請參閱[設定具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure.md)。
- 如需設定 Hive 原則和執行 Hive 查詢，請參閱[使用 ESP 針對 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)。
