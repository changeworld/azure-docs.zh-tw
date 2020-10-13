---
title: Azure HDInsight 的企業安全性套件
description: 瞭解 Azure HDInsight 中的企業安全性套件元件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 91fa6a8da555d0b0cc79b262a83306c1f72aa68a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567090"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Azure HDInsight 的企業安全性套件

企業安全性是選擇性的套件，您可以在建立叢集工作流程時，新增於 HDInsight 叢集上。 企業安全性套件支援：

* 與 Active Directory 整合以進行驗證。

    在過去，您已建立具有本機系統管理員使用者和本機 SSH 使用者的 HDInsight 叢集。 本機系統管理員使用者可以存取所有的檔案、資料夾、資料表和資料行。  使用企業安全性套件，您可以藉由整合 HDInsight 與您的 Azure Active Directory Domain Services 來啟用角色型存取控制。

    如需詳細資訊，請參閱

    * [已加入網域之 HDInsight 叢集的 Apache Hadoop 安全性簡介](./domain-joined/hdinsight-security-overview.md)

    * [規劃 HDInsight 中已加入網域的 Azure Apache Hadoop 叢集](./domain-joined/apache-domain-joined-architecture.md)

    * [設定已加入網域的沙箱環境](./domain-joined/apache-domain-joined-configure.md)

    * [使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* 資料的授權

  * 與 Apache Ranger 整合可取得 Hive、Spark SQL 和 Yarn 佇列的授權。
  * 您可以設定檔案和資料夾的存取控制。

    如需詳細資訊，請參閱 [在已加入網域的 HDInsight 中設定 Apache Hive 原則](./domain-joined/apache-domain-joined-run-hive.md)

* 檢視稽核記錄可監視存取和設定的原則。

## <a name="supported-cluster-types"></a>支援的叢集類型

目前，只有下列叢集類型支援企業安全性套件：

* Hadoop (僅限 HDInsight 3.6)
* Spark
* Kafka
* hbase
* 互動式查詢

## <a name="support-for-azure-data-lake-storage"></a>支援 Azure Data Lake Storage

企業安全性套件支援使用 Azure Data Lake Storage 同時作為主要儲存體和附加元件儲存體。

## <a name="pricing-and-service-level-agreement-sla"></a>定價和服務等級協定 (SLA) 

如需企業安全性套件之定價和 SLA 的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>後續步驟

* [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)
* [與 Azure HDInsight 版本相關聯的 Hortonworks 版本資訊](./hortonworks-release-notes.md)
* [HDInsight 上的 Apache 元件](./hdinsight-component-versioning.md)
