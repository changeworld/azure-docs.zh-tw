---
title: 使用 Azure HDInsight 解決群集創建失敗
description: 瞭解如何解決 Azure HDInsight 的 Apache 群集創建問題。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.openlocfilehash: 1e13c7ef8eae81ef2a12a8761b0596f6329f94dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937914"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>使用 Azure HDInsight 解決群集創建失敗

以下問題是群集創建失敗的最常見根本原因：

- 許可權問題
- 資源策略限制
- 防火牆
- 資源鎖定
- 不支援的元件版本
- 儲存體帳戶名稱限制
- 服務中斷

## <a name="permissions-issues"></a>權限問題

如果使用 Azure 資料湖存儲 Gen2，並且收到```AmbariClusterCreationFailedErrorCode```錯誤 ，```Internal server error occurred while processing the request. Please retry the request or contact support.```請打開 Azure 門戶，轉到存儲帳戶，並在存取控制 （IAM） 下，確保**存儲 Blob 資料參與者**或存儲 Blob**資料擁有者**角色已分配了對**訂閱的使用者分配的託管標識**的存取權限。 如需詳細指示，請參閱[在 Data Lake Storage Gen2 帳戶上設定受控識別的權限](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)。

如果使用 Azure 資料存儲第 1 代，請參閱[此處](../hdinsight-hadoop-use-data-lake-store.md)的設置和配置說明。 HBase 群集不支援資料存儲庫 Gen1，HDInsight 版本 4.0 不支援資料存儲存儲 Gen1。

如果使用 Azure 存儲，請確保存儲帳戶名稱在群集創建期間有效。

## <a name="resource-policy-restrictions"></a>資源策略限制

基於訂閱的 Azure 策略可能會拒絕創建公共 IP 位址。 建立 HDInsight 叢集需要兩個公用 IP。  

一般來說，下列原則可能會影響叢集的建立：

* 防止在訂閱中創建 IP 位址&負載等化器的策略。
* 阻止創建存儲帳戶的策略。
* 防止刪除網路資源（IP 位址/負載等化器）的策略。

## <a name="firewalls"></a>防火牆

虛擬網路或存儲帳戶上的防火牆可能會拒絕與 HDInsight 管理 IP 位址的通信。

允許來自下表中的 IP 位址的流量。

| 來源 IP 位址 | Destination | 方向 |
|---|---|---|
| 168.61.49.99 | *:443 | 輸入 |
| 23.99.5.239 | *:443 | 輸入 |
| 168.61.48.131 | *:443 | 輸入 |
| 138.91.141.162 | *:443 | 輸入 |

此外，還可以將特定于群集的區域的 IP 位址添加到其中。 有關每個 Azure 區域位址的清單，請參閱[HDInsight 管理 IP 位址](../hdinsight-management-ip-addresses.md)。

如果使用的是快速路由或您自己的自訂 DNS 伺服器，請參閱為[Azure HDInsight 規劃虛擬網路 - 連接多個網路](../hdinsight-plan-virtual-network-deployment.md#multinet)。

## <a name="resources-locks"></a>資源鎖  

確保[虛擬網路和資源組上沒有鎖](../../azure-resource-manager/management/lock-resources.md)。 如果資源組處於鎖定狀態，則無法創建或刪除群集。 

## <a name="unsupported-component-versions"></a>不支援的元件版本

確保您使用的是[受支援的 Azure HDInsight 版本](../hdinsight-component-versioning.md)和解決方案中的任何[Apache Hadoop 元件](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。  

## <a name="storage-account-name-restrictions"></a>儲存體帳戶名稱限制

儲存體帳戶名稱不可超過 24 個字元，且不可包含特殊字元。 這些限制也適用於儲存體帳戶中的預設容器名稱。

其他命名限制也適用于群集創建。 有關詳細資訊，請參閱[群集名稱限制](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)。

## <a name="service-outages"></a>服務中斷

檢查[Azure 狀態](https://status.azure.com)是否有任何潛在的中斷或服務問題。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 虛擬網路擴展 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [搭配使用 Azure 儲存體與 Azure HDInsight 叢集](../hdinsight-hadoop-use-blob-storage.md)
* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](../hdinsight-hadoop-provision-linux-clusters.md)
