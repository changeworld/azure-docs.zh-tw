---
title: 設定 Azure HDInsight 叢集的作業系統修補排程
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 7b789bf01a043b167d6740f09df935d9b683c48f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357754"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>針對以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程

> [!IMPORTANT]
> 在發佈的三個月內，Ubuntu 映射可供新的 Azure HDInsight 叢集建立。 執行中的叢集不會自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 最佳做法是，您可以在叢集建立後立即執行這些腳本動作並套用安全性更新。

HDInsight 提供可讓您在叢集上執行一般工作的支援，例如安裝 OS 修補程式、安全性更新和重新開機節點。 您可以使用下列兩個腳本來執行這些工作：以 [腳本動作](hdinsight-hadoop-customize-cluster-linux.md)的形式執行，並使用參數設定：

- `schedule-reboots.sh` -立即重新開機，或在叢集節點上排程重新開機。
- `install-updates-schedule-reboots.sh` -安裝所有更新，僅安裝核心 + 安全性更新，或只安裝核心更新。

> [!NOTE]  
> 腳本動作不會自動將更新套用到所有未來的更新週期。 請在每次必須套用新的更新時執行腳本，以安裝更新，然後重新開機 VM。

## <a name="preparation"></a>準備

在部署至生產環境之前，在具代表性的非生產環境上進行修補。 開發計畫，在實際修補之前充分測試您的系統。

從存留時間，與您的叢集的 ssh 會話，您可能會收到訊息，指出有可用的安全性更新。 這則訊息看起來可能像這樣：

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

修補是選擇性的，並由您自行決定。

## <a name="restart-nodes"></a>重新開機節點
  
腳本 [排程-重新開機](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)，設定將在叢集中的機器上執行的重新開機類型。 提交腳本動作時，請將它設定為套用到所有三個節點類型：前端節點、背景工作節點和 zookeeper。 如果腳本未套用至節點類型，則不會更新或重新開機該節點類型的 Vm。

`schedule-reboots script`接受一個數值參數：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要執行的重新開機類型 | 1 或 2 | 值為1可讓排程重新開機 (排程于12-24 小時) 。 值為2，可讓您在5分鐘內立即重新開機 () 。 如果未指定任何參數，則預設值為1。 |  

## <a name="install-updates-and-restart-nodes"></a>安裝更新並重新啟動節點

腳本 [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) 會提供選項來安裝不同類型的更新，並重新啟動 VM。

`install-updates-schedule-reboots`腳本接受兩個數值參數，如下表所述：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要安裝的更新類型 | 0、1或2 | 0值只會安裝核心更新。 值為1會安裝核心 + 安全性更新，2則會安裝所有更新。 如果未提供任何參數，則預設值為0。 |
| 要執行的重新開機類型 | 0、1或2 | 值為0會停用重新開機。 值為1會啟用排程重新開機，2則可立即重新開機。 如果未提供任何參數，則預設值為0。 使用者必須將輸入參數1變更為輸入參數2。 |

> [!NOTE]
> 將腳本套用至現有的叢集之後，您必須將它標示為已保存。 否則，透過調整作業所建立的新節點會使用預設的修補排程。 如果您將腳本套用為叢集建立程式的一部分，則會自動儲存此腳本。

> [!NOTE]
> 排程重新開機選項會自動輪流在12到24小時內重新開機已修補的叢集節點，並考慮高可用性、更新網域和容錯網域的考慮。 排定的重新開機不會終止執行中的工作負載，但當節點無法使用時，可能會在過渡期間捨棄叢集容量，進而導致處理時間變長。 

## <a name="next-steps"></a>後續步驟

如需使用腳本動作的特定步驟，請參閱下列各節，以 [使用腳本動作自訂以 Linux 為基礎的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md)叢集：

- [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
