---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564413"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-06112020"></a>發行日期：06/11/2020

此發行適用於 HDInsight 3.6 和 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到下列變更，請在數天內等待發行在您的區域中上線。

## <a name="new-features"></a>新功能
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 會使用 Azure 虛擬機器立即布建叢集。 在此版本中，新建立的 HDInsight 叢集會開始使用 Azure 虛擬機器擴展集。 變更會逐步推出。 您預期不會有重大變更。 深入瞭解[Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>重新開機 HDInsight 叢集中的 Vm
在此版本中，我們支援重新開機 HDInsight 叢集中的 Vm，以重新開機沒有回應的節點。 目前您只能透過 API 來執行此動作，PowerShell 和 CLI 支援也是如此。 如需有關 API 的詳細資訊，請參閱[此](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)檔。
 
## <a name="deprecation"></a>淘汰
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>淘汰 HDInsight 3.6 Spark 叢集中的 Spark 2.1 和 2.2
從 1 2020 年7月開始，客戶無法在 HDInsight 3.6 上使用 Spark 2.1 和2.2 建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>淘汰 HDInsight 4.0 Spark 叢集中的 Spark 2.3
從 1 2020 年7月開始，客戶將無法使用 Spark 2.3 on HDInsight 4.0 來建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Spark 2.4，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>淘汰 HDInsight 4.0 Kafka 叢集中的 Kafka 1.1
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 4.0 上使用 Kafka 1.1 建立新的 Kafka 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Kafka 2.1，以避免潛在的系統/支援中斷。
 
## <a name="behavior-changes"></a>行為變更
### <a name="esp-spark-cluster-head-node-size-change"></a>ESP Spark 叢集前端節點大小變更 
ESP Spark 叢集所允許的最小前端節點大小會變更為 Standard_D13_V2。 具有低核心和記憶體作為前端節點的 Vm，可能會因為 CPU 和記憶體容量相對較低而造成 ESP 叢集問題。 從版本開始，請使用高於 Standard_D13_V2 的 Sku，並 Standard_E16_V3 作為 ESP Spark 叢集的前端節點。
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>前端節點必須至少有 4 核心 VM 
前端節點必須至少有 4 核心 VM，才能確保 HDInsight 叢集的高可用性和可靠性。 自 2020 年 4 月 6 日起，客戶只能選擇 4 核心或以上的 VM 作為新 HDInsight 叢集的前端節點。 現有的叢集將會繼續如預期般執行。 
 
### <a name="cluster-worker-node-provisioning-change"></a>叢集背景工作節點布建變更
當80% 的背景工作節點準備就緒時，叢集會進入**操作**階段。 在這個階段，客戶可以執行所有的資料平面作業，例如執行腳本和工作。 但客戶無法執行任何控制平面作業，例如相應增加/減少。 只支援刪除。
 
在**操作**階段之後，叢集會等待剩餘20% 背景工作角色節點有另一個60分鐘的時間。 在此60分鐘結束時，叢集會移至**執行中的階段，** 即使所有背景工作節點仍無法使用。 一旦叢集進入**執行階段之後**，您就可以像平常一樣使用它。 這兩個控制計畫作業（例如相應增加/減少）和通話方案作業（例如執行腳本和工作）都已被接受。 如果部分要求的背景工作節點無法使用，叢集將會標示為「部分成功」。 系統會針對已成功部署的節點向您收費。 
 
### <a name="create-new-service-principal-through-hdinsight"></a>透過 HDInsight 建立新的服務主體
先前在建立叢集時，客戶可以建立新的服務主體，以在 Azure 入口網站中存取已連線的 ADLS Gen 1 帳戶。 自 15 2020 年6月起，客戶無法在 HDInsight 建立工作流程中建立新的服務主體，只支援現有的服務主體。 請參閱[使用 Azure Active Directory 建立服務主體和憑證](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

### <a name="time-out-for-script-actions-with-cluster-creation"></a>建立叢集的腳本動作的時間輸出
HDInsight 支援在建立叢集時執行腳本動作。 在此版本中，所有具有叢集建立的腳本動作都必須在**60 分鐘**內完成，否則就會超時。提交至執行中叢集的腳本動作不會受到影響。 如需詳細資訊，請參閱[此處](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process)。
 
## <a name="upcoming-changes"></a>即將推出的變更
您不需要留意即將推出的重大變更。
 
## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 
 
## <a name="component-version-change"></a>元件版本變更
### <a name="hbase-20-to-216"></a>HBase 2.0 升級至 2.1.6
HBase 版本會從2.0 版升級至2.1.6。
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 升級至 2.4.4
Spark 版本會從版本2.4.0 升級至2.4.4。
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 升級至 2.1.1
Kafka 版本已從版本2.1.0 升級至2.1.1。
 
您可以在[本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到 hdinsight 4.0 ad hdinsight 3.6 的目前元件版本

## <a name="known-issues"></a>已知問題

### <a name="hive-warehouse-connector-issue"></a>Hive 倉儲連接器問題
此版本中的 Hive 倉儲連接器發生問題。 下一版將會包含修正程式。 在此版本之前建立的現有叢集不會受到影響。 盡可能避免 droping 並重新建立叢集。 如果您需要進一步協助，請開啟支援票證。
