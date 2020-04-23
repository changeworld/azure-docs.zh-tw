---
title: Apache Hadoop 元件和版本 - Azure HDInsight
description: 在 Azure HDInsight 中瞭解 Apache Hadoop 元件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084765"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？

瞭解 Microsoft Azure HDInsight 中的[Apache Hadoop](https://hadoop.apache.org/)環境元件和版本以及企業安全包。 此外，了解如何檢查 HDInsight 中的 Hadoop 元件版本。

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>可以搭配不同 HDInsight 版本使用的 Apache Hadoop 元件

Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 2017 年 4 月 4 日,Azure HDInsight 使用的預設群集版本為 3.6。

下表列出與 HDInsight 叢集版本相關聯的元件版本：

> [!NOTE]  
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure 傳統 CLI 建立叢集時指定 HDInsight 版本。

| 元件              | HDInsight 4.0 | HDInsight 3.6 (預設)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 和 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0(非ESP群集),1.2.1(ESP群集)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase (英文)           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1、1.0 *(請參閱下方注意事項) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 基於系統效能考量，Kafka 0.10 版的支援已在 2019 年 3 月過期。

## <a name="check-for-current-hadoop-component-version-information"></a>檢查目前的 Hadoop 元件版本資訊

與 HDInsight 群集版本關聯的 Hadoop 環境元件版本可能會隨著 HDInsight 的更新而更改。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱 [Apache Ambari 文件](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

### <a name="release-notes"></a>版本資訊

請參閱 [HDInsight 版本資訊](hdinsight-release-notes.md) ，以取得 HDInsight 最新版本的其他版本資訊。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>支援 HDInsight 版本的過期和停用

**支援過期**意味著 Microsoft 將不再為指定的 HDInsight 版本提供支援。 並且它將不再可以通過 Azure 門戶進行群集創建。 但是,這些版本仍可以使用 Azure CLI 或各種 SDK 創建。

**停用**HDInsight 版本意味著現有群集將繼續以現有方式運行。 但是,無法通過任何方法(包括 CLI 和 SDK)創建此版本的新群集。 其他控制平面功能(如手動縮放和自動縮放)在版本停用后也可能無法正常工作。 支援不適用於已停用的版本。

下表列出 HDInsight 的版本。 如果已知支援到期日與停用日期，也會一併提供。

### <a name="available-versions"></a>可用的版本

下表列出了 Azure 門戶中可用的 HDInsight 版本以及其他部署方法(如 PowerShell 和 .NET SDK)。

| HDInsight 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 門戶中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020年12月31日 |2020年12月31日 |是 |是 |

Spark 2.1,2.2 &卡夫卡 1.0 支援將於 2020 年 6 月 30 日到期。

> [!NOTE]  
> 當版本的支援到期之後，可能就無法透過 Microsoft Azure 入口網站取得它。 不過，您可透過 Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 命令中的 `Version` 參數和 .NET SDK 持續取得叢集版本，直到版本停用日期為止。

### <a name="retired-versions"></a>已淘汰的版本

下表列出了 Azure 門戶中**不可用的**HDInsight 版本。

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

    過去,您使用本地管理員使用者和本地 SSH 使用者創建了 HDInsight 群集。 本機系統管理員使用者可以存取所有的檔案、資料夾、資料表和資料行。  借助企業安全包,您可以通過將 HDInsight 與活動目錄集成,實現基於角色的訪問控制。 其中包括本地活動目錄、Azure 活動目錄域服務。 或 IaaS 虛擬機器上的活動目錄。 群集上的域管理員可以授予使用者使用自己的公司(域)使用者名和密碼。

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

### <a name="pricing-and-service-level-agreement-sla"></a>定價和服務等級協定 (SLA)

如需企業安全性套件之定價和 SLA 的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 叢集版本的服務等級協定

服務等級協定 (SLA) 定義為_支援視窗_。 支援視窗是 HDInsight`Microsoft Customer Service and Support`版本受 支持的時間段。 如果版本已通過_支援到期日期_,則 HDInsight 群集位於支援視窗之外。 支援 HDInsight 版本 X(在較新的 X+1 版本可用後)支援過期,其後一版為:  

- 公式 1：將 HDInsight 叢集版本 X 的發行日期加上 180 天。
- 公式 2：將 Azure 入口網站中推出 HDInsight 叢集版本 X+1 的日期加上 90 天。

_停用日期_是無法在 HDInsight 上創建群集版本的日期。 從 2017 年 7 月 31 日起,您無法在 HDInsight 群集的停用日期後調整其大小。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小

有關為群組選擇哪個虛擬機器 SKU 的詳細資訊,請參閱 Azure [HDInsight 叢集設定詳細資訊](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)
- [霍頓工程發行筆記與 Azure HDInsight 版本關聯](./hortonworks-release-notes.md)
