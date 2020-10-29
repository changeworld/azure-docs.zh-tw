---
title: Apache Hadoop 元件和版本 - Azure HDInsight
description: 了解 Azure HDInsight 中的 Apache Hadoop 元件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 282f13c89c527b096573b67b5d539de821e89032
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900798"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>可供 Azure HDInsight 使用的 Apache Hadoop 元件和版本

在本文中，您將了解 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 環境元件和版本，以及企業安全性套件。 此外，您也會了解如何檢查 HDInsight 中的 Hadoop 元件版本。

## <a name="apache-components-available-with-different-hdinsight-versions"></a>可以搭配不同 HDInsight 版本使用的 Apache 元件

Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 在 2017 年 4 月 4 日，Azure HDInsight 預設使用的叢集版本為 3.6 版。

下表列出與 HDInsight 叢集版本相關聯的元件版本。

> [!NOTE]
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure 傳統 CLI 建立叢集時，指定 HDInsight 版本。

| 元件              | HDInsight 4.0 | HDInsight 3.6 (預設值)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 和 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP 互動式查詢) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase (英文)           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0.5           | 0.4。                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0：完全支援 Spark 2.4 和 Kafka 2.1。 但是，不會提供 Spark 2.3 和 Kafka 1.1 叢集類型。 HDInsight 3.6：完全支援 Spark 2.3 和 Kafka 1.1。  

## <a name="check-for-current-apache-component-version-information"></a>檢查目前的 Apache 元件版本資訊

在 HDInsight 的更新中，可能會變更與 HDInsight 叢集版本相關聯的 Hadoop 環境元件版本。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱 [Apache Ambari 文件](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) \(英文\)。

### <a name="release-notes"></a>版本資訊

如需 HDInsight 最新版本的其他版本資訊，請參閱 [HDInsight 版本資訊](hdinsight-release-notes.md)。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 版本的支援到期和淘汰

**支援到期** 表示 Microsoft 不再提供特定 HDInsight 版本的支援。 且無法再透過 Azure 入口網站來建立叢集。 您仍然可以使用 Azure CLI 或各種 SDK 來建立這些版本。

**淘汰** 表示 HDInsight 版本的現有叢集會繼續以原狀執行。 此版本的新叢集無法透過任何方法建立，其中包括 CLI 和 SDK。 其他控制平面功能 (例如手動縮放和自動調整) 在淘汰版本後也可能無法使用。 已淘汰的版本不提供支援。

下表列出 HDInsight 的版本。 如果已知支援到期日與淘汰日期，也會一併提供。

### <a name="available-versions"></a>可用的版本

此表列出 Azure 入口網站中可用的 HDInsight 版本，以及透過諸如 PowerShell 和 .NET SDK 等其他部署方法。

| HDInsight 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站中的可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |是 |是 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日      | * 2021 年6月30日 |2021年6月30日 |是 |是 |

* 我們即將擴充特定 HDInsight 3.6 叢集類型的支援時間範圍

| 叢集類型                    | Framework 版本 | 目前的支援到期日        | 新的支援到期日 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 Spark             | 2.3               | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 Spark             | 2.2               | 于2020年6月30日淘汰          |                             |
| HDInsight 3.6 Spark             | 2.1               | 于2020年6月30日淘汰          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 Kafka             | 1.0               | 于2020年6月30日淘汰。         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 Interactive Query | 2.1               | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 風暴             | 1.1               | 2020年12月31日                      | 2021年6月30日               |
| HDInsight 3.6 ML 服務      | 9.3               | 2020年12月31日                      | 2020年12月31日                |

> [!NOTE]
> 叢集版本的支援到期後，可能無法透過 Azure 入口網站使用。 在某些情況下，您可以使用 Windows PowerShell [>new-azhdinsightcluster](/powershell/module/az.hdinsight/new-azhdinsightcluster)命令中的 **version** 參數和 .net SDK 來繼續使用叢集版本，直到版本停用日期為止。

### <a name="retired-versions"></a>已淘汰的版本

此表列出不可在 Azure 入口網站中取得的 HDInsight 版本。

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

服務等級協定會定義為「支援期間」。 支援期間是 Microsoft 客戶服務與支援中心支援 HDInsight 版本的一段時間。 如果已超過版本的「支援到期日」，則表示該 HDInsight 叢集不在支援期間內。 HDInsight 版本 X (在較新的 X+1 版本推出後) 的支援到期日會以下列較晚的時間為準：

- **公式 1：** 將 HDInsight 叢集版本 X 的發行日期加上 180 天。
- **公式 2：** 將 Azure 入口網站中推出 HDInsight 叢集版本 X+1 的日期加上 90 天。

「淘汰日期」是在此日期之後便無法在 HDInsight 上建立叢集版本。 2017 年 7 月 31 日開始，您就無法在 HDInsight 叢集的淘汰日期之後調整叢集大小。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小

如需為您叢集選取哪個虛擬機器 SKU 的詳細資訊，請參閱 [Azure HDInsight 叢集組態詳細資料](hdinsight-supported-node-configuration.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [企業安全性套件](./enterprise-security-package.md)
- [與 Azure HDInsight 版本相關聯的 Hortonworks 版本資訊](./hortonworks-release-notes.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)