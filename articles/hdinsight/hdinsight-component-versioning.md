---
title: Apache Hadoop 元件和版本 - Azure HDInsight
description: 瞭解 Azure HDInsight 中的 Apache Hadoop 元件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124647"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>適用于 Azure HDInsight 的 Apache 元件和版本

在本文中，您將瞭解 Azure HDInsight 和企業安全性套件中的[Apache Hadoop](https://hadoop.apache.org/)環境元件和版本。 您也會瞭解如何在 HDInsight 中檢查 Hadoop 元件版本。

## <a name="apache-components-available-with-different-hdinsight-versions"></a>可搭配不同 HDInsight 版本使用的 Apache 元件

Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 2017年4月4日，Azure HDInsight 使用的預設叢集版本是3.6。

下表列出與 HDInsight 叢集版本相關聯的元件版本。

> [!NOTE]
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure 傳統 CLI 建立叢集時，指定 HDInsight 版本。

| 元件              | HDInsight 4.0 | HDInsight 3.6 (預設值)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 和 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 （2.1.0 on ESP 互動式查詢） |
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
| Apache Kafka           | 1.1.1，2。1    | 1.1、1.0 * （請參閱附注）。   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | iisnode-inspector-0.7.3.dll                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Kafka 版本0.10 的支援將于3月2019日過期，因為系統效能考慮。

## <a name="check-for-current-apache-component-version-information"></a>檢查目前的 Apache 元件版本資訊

與 hdinsight 叢集版本相關聯的 Hadoop 環境元件版本可能會隨著 HDInsight 的更新而變更。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱[Apache Ambari 檔](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

### <a name="release-notes"></a>版本資訊

如需 HDInsight 最新版本的其他版本資訊，請參閱[hdinsight 版本](hdinsight-release-notes.md)資訊。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 版本的支援到期和淘汰

**支援到期**表示 Microsoft 不再提供特定 HDInsight 版本的支援。 而且無法再透過 Azure 入口網站來建立叢集。 您仍然可以使用 Azure CLI 或各種 Sdk 來建立這些版本。

「**淘汰**」表示 HDInsight 版本的現有叢集會繼續以自己的身分執行。 此版本的新叢集無法透過任何方法建立，其中包括 CLI 和 Sdk。 其他控制平面功能（例如手動縮放和自動調整）在淘汰版本後也可能無法使用。 已淘汰的版本不提供支援。

下表列出 HDInsight 的版本。 在已知的情況中，也會提供支援到期和停用日期。

### <a name="available-versions"></a>可用的版本

下表列出可在 Azure 入口網站中使用的 HDInsight 版本，以及透過 PowerShell 和 .NET SDK 等其他部署方法。

| HDInsight 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020年12月31日 |2020年12月31日 |是 |是 |

Spark 2.1、2.2 和 Kafka 1.0 支援將于2020年6月30日到期。

> [!NOTE]
> 版本的支援到期後，可能無法透過 Azure 入口網站使用。 使用 Windows PowerShell [new-azhdinsightcluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)命令中的**version**參數和 .net SDK （直到版本停用日期），即可繼續取得叢集版本。

### <a name="retired-versions"></a>已淘汰的版本

下表列出 Azure 入口網站中無法使用的 HDInsight 版本。

| HDInsight 版本 | HDP 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站中的可用性 |
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
> 依預設，系統會為 HDInsight 2.1 和更新版本部署具有兩個前端節點的高可用性叢集。 它們不適用於 HDInsight 1.6 版的叢集。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 叢集版本的服務等級協定

服務等級協定會定義為_支援視窗_。 支援視窗是 Microsoft 客戶服務及支援的 HDInsight 版本所支援的時間週期。 如果版本已通過_支援到期日_，則 HDInsight 叢集會在支援視窗外。 HDInsight 版本 X 的支援到期（提供較新的 X + 1 版本之後）是後面的：

- **公式1：** 將180天新增至 HDInsight 叢集版本 X 的發行日期。
- **公式2：** 在 Azure 入口網站中提供 HDInsight 叢集版本 X + 1 的日期加上90天。

_停用日期_是無法在 HDInsight 上建立叢集版本之後的日期。 從2017年7月31日起，您無法在 HDInsight 叢集淘汰日期之後調整其大小。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小

如需有關為您的叢集選取哪些虛擬機器 Sku 的詳細資訊，請參閱 Azure HDInsight 叢集設定[詳細資料](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)
- [Hortonworks 與 Azure HDInsight 版本相關聯的版本資訊](./hortonworks-release-notes.md)
- [企業安全性套件](./enterprise-security-package.md)