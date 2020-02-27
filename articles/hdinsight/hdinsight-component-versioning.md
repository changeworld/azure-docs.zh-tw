---
title: Apache Hadoop 元件和版本 - Azure HDInsight
description: 瞭解 Azure HDInsight 中的 Apache Hadoop 元件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 9c801f5c039996ac36c4f4479318d64cccdd204d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649988"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？

了解 Microsoft Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 生態系統元件和版本，以及企業安全性套件。 此外，了解如何檢查 HDInsight 中的 Hadoop 元件版本。

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>可以搭配不同 HDInsight 版本使用的 Apache Hadoop 元件

Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 從2017年4月4日起，Azure HDInsight 使用的預設叢集版本為3.6。

下表列出與 HDInsight 叢集版本相關聯的元件版本：

> [!NOTE]  
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure 傳統 CLI 建立叢集時指定 HDInsight 版本。

| 元件              | HDInsight 4.0 | HDInsight 3.6 (預設)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 和 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0，1.2。1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase (英文)           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1、2。4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4、0.4、0。3               |
| Apache Kafka           | 1.1.1，2。1    | 1.1、1.0 *(請參閱下方注意事項) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | iisnode-inspector-0.7.3.dll                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 基於系統效能考量，Kafka 0.10 版的支援已在 2019 年 3 月過期。

## <a name="check-for-current-hadoop-component-version-information"></a>檢查目前的 Hadoop 元件版本資訊

在 HDInsight 的更新中，可能會變更與 HDInsight 叢集版本相關聯的 Hadoop 生態系統元件版本。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱[Apache Ambari 檔](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

### <a name="release-notes"></a>版本資訊

請參閱 [HDInsight 版本資訊](hdinsight-release-notes.md) ，以取得 HDInsight 最新版本的其他版本資訊。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 版本的支援到期和淘汰

「**支援到期**」表示 Microsoft 將不再提供指定之 HDInsight 版本的支援，而且不會再透過 Azure 入口網站建立叢集來可用。 不過，您仍然可以使用 Azure CLI 或各種 Sdk 來建立這些版本。 

**淘汰**HDInsight 版本表示現有的叢集將繼續依自己的方式執行。 不過，此版本的新叢集無法透過任何方式（包括 CLI 和 Sdk）來建立。 在淘汰版本之後，其他控制平面功能（例如手動縮放和自動調整）可能也無法使用。 已淘汰的版本不提供支援。

下表列出 HDInsight 的版本。 如果已知支援到期日與停用日期，也會一併提供。

### <a name="available-versions"></a>可用的版本

下表列出 Azure 入口網站中可用的 HDInsight 版本，以及諸如 PowerShell 和 .NET SDK 等其他部署方法。

| HDInsight 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020年12月31日 |2020年12月31日 |是 |是 |

Spark 2.1、2.2 & Kafka 1.0 支援將于2020年6月30日到期。

> [!NOTE]  
> 當版本的支援到期之後，可能就無法透過 Microsoft Azure 入口網站取得它。 不過，您可透過 Windows PowerShell `Version`New-AzHDInsightCluster[ 命令中的 ](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 參數和 .NET SDK 持續取得叢集版本，直到版本停用日期為止。

### <a name="retired-versions"></a>已淘汰的版本

下表列出**不**可在 Azure 入口網站中取得的 HDInsight 版本。

| HDInsight 版本 | HDP 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站上的可用性 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016 年 9 月 30 日 |2017 年 9 月 5 日 |2018 年 6 月 28 日 |是 |否 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016 年 3 月 29 日 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |是 |否 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2018 年 7 月 31 日 |是 |否 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |是 |否 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 或 Windows Server 2012 R2 |2015 年 2 月 18 日 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |是 |否 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014 年 6 月 24 日 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |是 |否 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014 年 2 月 11 日 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |是 |否 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013 年 10 月 28 日 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |是 |否 |
| HDInsight 1.6 |HDP 1.1 | |2013 年 10 月 28 日 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |否 |否 |

> [!NOTE]  
> 依預設，系統會為 HDInsight 2.1 和更新版本部署具有兩個前端節點的高可用性叢集。 它們不適用於 HDInsight 版本 1.6 叢集。

## <a name="enterprise-security-package-for-hdinsight"></a>適用於 HDInsight 的企業安全性套件

企業安全性是選擇性的套件，您可以在建立叢集工作流程時，新增於 HDInsight 叢集上。 企業安全性套件支援：

- 與 Active Directory 整合以進行驗證。

    在過去，您只能使用本機系統管理員使用者和本機 SSH 使用者來建立 HDInsight 叢集。 本機系統管理員使用者可以存取所有的檔案、資料夾、資料表和資料行。  您可以利用企業安全性套件來啟用角色型存取控制，方法是將 HDInsight 叢集與您自己的 Active Directory 整合，叢集中會包含 IaaS 虛擬機器上的內部部署 Active Directory、Azure Active Directory Domain Services 或 Active Directory。 叢集上的網域系統管理員可以授與使用者使用自己公司 (網域) 的使用者名稱和密碼來存取叢集。

    如需詳細資訊，請參閱

    - [已加入網域之 HDInsight 叢集的 Apache Hadoop 安全性簡介](./domain-joined/hdinsight-security-overview.md)
    - [規劃 HDInsight 中已加入網域的 Azure Apache Hadoop 叢集](./domain-joined/apache-domain-joined-architecture.md)
    - [設定已加入網域的沙箱環境](./domain-joined/apache-domain-joined-configure.md)
    - [使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- 資料的授權

  - 與 Apache Ranger 整合可取得 Hive、Spark SQL 和 Yarn 佇列的授權。
  - 您可以設定檔案和資料夾的存取控制。

    如需詳細資訊，請參閱

  - [在已加入網域的 HDInsight 中設定 Apache Hive 原則](./domain-joined/apache-domain-joined-run-hive.md)

- 檢視稽核記錄可監視存取和設定的原則。

### <a name="supported-cluster-types"></a>支援的叢集類型

目前，只有下列叢集類型支援企業安全性套件：

- Hadoop (僅限 HDInsight 3.6)
- Spark
- Kafka
- hbase
- 互動式查詢

### <a name="support-for-azure-data-lake-storage"></a>支援 Azure Data Lake Storage

企業安全性套件支援使用 Azure Data Lake Storage 同時作為主要儲存體和附加元件儲存體。

### <a name="pricing-and-service-level-agreement"></a>定價和服務等級協定

如需企業安全性套件之定價和 SLA 的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 叢集版本的服務等級協定

服務等級協定 (SLA) 是根據「支援期間」來定義。 支援期間是 Microsoft 客戶服務與支援中心支援 HDInsight 叢集版本的一段時間。 如果已超過版本的「支援到期日」，則表示該 HDInsight 叢集不在支援期間內。 特定 HDInsight 版本 X (在較新的 X+1 版本推出後) 的支援到期日計算方式會以下列較晚的時間為準：  

- 公式 1：將 HDInsight 叢集版本 X 的發行日期加上 180 天。
- 公式 2：將 Azure 入口網站中推出 HDInsight 叢集版本 X+1 的日期加上 90 天。

_停用日期_是無法在 HDInsight 上建立叢集版本之後的日期。 從2017年7月31日開始，您無法在 HDInsight 叢集淘汰日期之後調整其大小。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>與 HDInsight 版本相關聯的 Hortonworks 版本資訊

本節提供 Hortonworks Data Platform 發佈的版本資訊，以及與 HDInsight 搭配使用的 Apache 元件。
* HDInsight 叢集 4.0 版採用以 [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) 為基礎的 Hadoop 散發
* HDInsight 叢集 3.6 版採用以 [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.5 版採用以 [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) 為基礎的 Hadoop 散發套件。 HDInsight 叢集版本 3.5 是在 Azure 入口網站中建立的「預設」 Hadoop 叢集。
* HDInsight 叢集 3.4 版採用以 [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.3 版採用以 [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)為基礎的 Hadoop 散發。

  * [Apache Storm 版本資訊](https://storm.apache.org/2015/11/05/storm0100-released.html) \(英文\) 可從 Apache 網站上取得。
  * [Apache Hive 版本資訊](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) \(英文\) 可從 Apache 網站上取得。
* HDInsight 叢集版本3.2 使用以[Hortonworks 資料平臺 2.2][hdp-2-2]為基礎的 Hadoop 散發套件。

  * 特定 Apache 元件的可用版本資訊如下：[Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) 及 [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight 叢集版本3.1 使用以[Hortonworks 資料平臺 2.1.7][hdp-2-1-7]為基礎的 Hadoop 散發套件。 在11月 7 2014 日之前建立的 HDInsight 3.1 叢集是以[Hortonworks 資料平臺 2.1.1][hdp-2-1-1]為基礎。
* HDInsight 叢集版本3.0 使用以[Hortonworks 資料平臺 2.0][hdp-2-0-8]為基礎的 Hadoop 散發套件。
* HDInsight 叢集版本2.1 使用以[Hortonworks 資料平臺 1.3][hdp-1-3-0]為基礎的 Hadoop 散發套件。
* HDInsight 叢集版本1.6 使用以[Hortonworks 資料平臺 1.1][hdp-1-1-0]為基礎的 Hadoop 散發套件。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小

如需有關為您的叢集選取哪些虛擬機器 Sku 的詳細資訊，請參閱 Azure HDInsight 叢集設定[詳細資料](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
