---
title: 為 Azure HDInsight 叢集設定作業系統修補排程
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206855"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程

> [!IMPORTANT]
> Ubuntu 映射會在發行後的三個月內，提供新的 Azure HDInsight 叢集建立。 從2019年1月起，執行中的叢集不會自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

HDInsight 提供支援，讓您在叢集上執行一般工作，例如安裝 OS 修補程式、安全性更新和重新開機節點。 這些工作是使用下列兩個腳本來完成，可以用[腳本動作](hdinsight-hadoop-customize-cluster-linux.md)執行，並使用參數來設定：

- `schedule-reboots.sh`-立即重新開機，或在叢集節點上排程重新開機。
- `install-updates-schedule-reboots.sh`-安裝所有更新、僅限核心 + 安全性更新，或只安裝核心更新。

> [!NOTE]  
> 腳本動作不會針對所有未來的更新週期自動套用更新。 每次必須套用新的更新以安裝更新，然後重新開機 VM 時，執行腳本。

## <a name="preparation"></a>準備

在部署至生產環境之前，在代表性非生產環境中進行修補。 開發計畫，在實際修補之前充分測試您的系統。

從您的叢集的 ssh 會話開始，您可能會收到一則訊息，指出有可用的升級。 訊息看起來可能像這樣：

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

修補是選擇性的，且由您自行決定。

## <a name="restart-nodes"></a>重新開機節點
  
腳本[排程-重新開機](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)，設定將在叢集中的電腦上執行的重新開機類型。 提交腳本動作時，請將它設定為適用于這三個節點類型：前端節點、背景工作節點和 zookeeper。 如果腳本未套用至節點類型，則不會更新或重新開機該節點類型的 Vm。

`schedule-reboots script`接受一個數值參數：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要執行的重新開機類型 | 1 或 2 | 值為1會啟用排程重新開機（排程時間為12-24 小時）。 值為2時，會立即重新開機（5分鐘）。 如果未指定參數，預設值為1。 |  

## <a name="install-updates-and-restart-nodes"></a>安裝更新並重新啟動節點

腳本[install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh)會提供安裝不同類型更新的選項，並重新啟動 VM。

`install-updates-schedule-reboots`腳本接受兩個數值參數，如下表所述：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要安裝的更新類型 | 0、1或2 | 值為0時，只會安裝核心更新。 值為1會安裝所有更新，而2只會安裝核心 + 安全性更新。 如果未提供任何參數，則預設值為0。 |
| 要執行的重新開機類型 | 0、1或2 | 值為0會停用重新開機。 值為1會啟用排程重新開機，2則可立即重新開機。 如果未提供任何參數，則預設值為0。 使用者必須將輸入參數1變更為輸入參數2。 |

> [!NOTE]
> 將腳本套用至現有的叢集之後，您必須將它標示為已保存。 否則，透過調整作業所建立的新節點會使用預設的修補排程。 如果您將腳本套用為叢集建立程式的一部分，則會自動儲存。

## <a name="next-steps"></a>後續步驟

如需使用腳本動作的特定步驟，請參閱[使用腳本動作自訂以 Linux 為基礎的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md)叢集中的下列各節：

- [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
