---
title: 使用 Azure HDInsight 針對叢集建立失敗進行疑難排解
description: 瞭解如何針對 Azure HDInsight 的 Apache 叢集建立問題進行疑難排解。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: e12b96883ae26b6c10e3622c35914ce498afca48
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944427"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>使用 Azure HDInsight 針對叢集建立失敗進行疑難排解

下列問題是叢集建立失敗最常見的根本原因：

- 許可權問題
- 資源原則限制
- 防火牆
- 資源鎖定
- 不支援的元件版本
- 儲存體帳戶名稱限制
- 服務中斷

## <a name="permissions-issues"></a>權限問題

如果您使用 Azure Data Lake Storage Gen2，並收到錯誤 `AmbariClusterCreationFailedErrorCode` ： " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: "，請開啟 Azure 入口網站，移至您的儲存體帳戶，然後在 [存取控制] (IAM) 下，確定 **儲存體 blob 資料參與者** 或 **儲存體 blob 資料擁有** 者角色已指派訂用帳戶的 **使用者指派受控識別** 存取權。 如需詳細指示，請參閱在 [Data Lake Storage Gen2 上設定受控識別的許可權](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) 。

如果您使用 Azure Data Lake Storage Gen1，請參閱安裝和設定指示 [搭配使用 Azure Data Lake Storage Gen1 與 Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)叢集。 HBase 叢集不支援 Data Lake Storage Gen1，且在 HDInsight 4.0 版中不支援。

如果使用 Azure 儲存體，請確定儲存體帳戶名稱在叢集建立期間是有效的。

## <a name="resource-policy-restrictions"></a>資源原則限制

以訂用帳戶為基礎的 Azure 原則可能會拒絕建立公用 IP 位址。 建立 HDInsight 叢集需要兩個公用 IP。  

一般而言，下列原則可能會影響叢集的建立：

* 防止在訂用帳戶內建立 IP 位址 & 負載平衡器的原則。
* 防止建立儲存體帳戶的原則。
* 防止 (IP 位址/Load 平衡器) 刪除網路資源的原則。

## <a name="firewalls"></a>防火牆

虛擬網路或儲存體帳戶上的防火牆可能會拒絕與 HDInsight 管理 IP 位址的通訊。

允許來自下表中 IP 位址的流量。

| 來源 IP 位址 | 目的地 | 方向 |
|---|---|---|
| 168.61.49.99 | *:443 | 連入 |
| 23.99.5.239 | *:443 | 連入 |
| 168.61.48.131 | *:443 | 連入 |
| 138.91.141.162 | *:443 | 連入 |

也請新增叢集建立所在區域的特定 IP 位址。 如需每個 Azure 區域的地址清單，請參閱 [HDInsight 管理 IP 位址](../hdinsight-management-ip-addresses.md) 。

如果您使用 express route 或您自己的自訂 DNS 伺服器，請參閱 [規劃虛擬網路以 Azure HDInsight 連接多個網路](../hdinsight-plan-virtual-network-deployment.md#multinet)。

## <a name="resources-locks"></a>資源鎖定  

確定 [您的虛擬網路和資源群組上](../../azure-resource-manager/management/lock-resources.md)沒有任何鎖定。 如果已鎖定資源群組，就無法建立或刪除叢集。 

## <a name="unsupported-component-versions"></a>不支援的元件版本

確定您使用的是 [支援的 Azure HDInsight 版本](../hdinsight-component-versioning.md) ，以及解決方案中的任何 [Apache Hadoop 元件](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) 。  

## <a name="storage-account-name-restrictions"></a>儲存體帳戶名稱限制

儲存體帳戶名稱不可超過 24 個字元，且不可包含特殊字元。 這些限制也適用於儲存體帳戶中的預設容器名稱。

其他命名限制也適用于建立叢集。 如需詳細資訊，請參閱 [群集名稱限制](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)。

## <a name="service-outages"></a>服務中斷

檢查 [Azure 狀態](https://status.azure.com) 是否有任何可能的中斷或服務問題。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 虛擬網路延伸 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [搭配使用 Azure 儲存體與 Azure HDInsight 叢集](../hdinsight-hadoop-use-blob-storage.md)
* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](../hdinsight-hadoop-provision-linux-clusters.md)
