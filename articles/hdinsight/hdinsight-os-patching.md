---
title: 為 Azure HDInsight 群集配置作業系統修補計畫
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206855"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>為基於 Linux 的 HDInsight 群集配置作業系統修補計畫

> [!IMPORTANT]
> Ubuntu 映射可在發佈後的三個月內用於新的 Azure HDInsight 群集創建。 截至 2019 年 1 月，正在運行的群集不會自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

HDInsight 支援您在群集上執行常見任務，例如安裝 OS 修補程式、安全更新和重新開機節點。 這些任務使用以下兩個腳本完成，這些腳本可以作為[腳本操作](hdinsight-hadoop-customize-cluster-linux.md)運行，並使用參數進行配置：

- `schedule-reboots.sh`- 立即重新開機，或安排在叢集節點上重新開機。
- `install-updates-schedule-reboots.sh`- 安裝所有更新，僅安裝內核 + 安全更新，或僅安裝內核更新。

> [!NOTE]  
> 腳本操作不會自動應用所有將來更新週期的更新。 每次必須應用新更新來安裝更新時，請運行腳本，然後重新開機 VM。

## <a name="preparation"></a>準備

在部署到生產之前，在具有代表性的非生產環境中進行修補。 制定計畫，在實際修補之前充分測試系統。

從與群集的 ssh 會話中，您可能會不時收到一條消息，指出升級可用。 消息可能如下所示：

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

修補是可選的，由您自行決定。

## <a name="restart-nodes"></a>重新開機節點
  
腳本[計畫重新開機](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)，設置將在群集中的電腦上執行的重新開機類型。 提交腳本操作時，將其設置為應用於所有三種節點類型：頭節點、輔助節點和動物園管理員。 如果腳本未應用於節點類型，則不會更新或重新開機該節點類型的 VM。

接受`schedule-reboots script`一個數位參數：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要執行的重新開機類型 | 1 或 2 | 值 1 支援計畫重新開機（計畫在 12-24 小時內）。 值為 2 可立即重新開機（5 分鐘內）。 如果未給出參數，則預設值為 1。 |  

## <a name="install-updates-and-restart-nodes"></a>安裝更新並重新啟動節點

腳本[install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh)提供了安裝不同類型的更新和重新開機 VM 的選項。

腳本`install-updates-schedule-reboots`接受兩個數字參數，如下表所述：

| 參數 | 接受的值 | 定義 |
| --- | --- | --- |
| 要安裝的更新類型 | 0、1 或 2 | 值 0 僅安裝內核更新。 值 1 安裝所有更新，2 個僅安裝內核 + 安全更新。 如果未提供參數，則預設值為 0。 |
| 要執行的重新開機類型 | 0、1 或 2 | 值 0 禁用重新開機。 值 1 支援計畫重新開機，2 支援立即重新開機。 如果未提供參數，則預設值為 0。 使用者必須將輸入參數 1 更改為輸入參數 2。 |

> [!NOTE]
> 將腳本應用於現有群集後，必須將其標記為已保留。 否則，透過調整作業所建立的新節點會使用預設的修補排程。 如果將腳本應用為群集創建過程的一部分，則該腳本將自動保留。

## <a name="next-steps"></a>後續步驟

有關使用腳本操作的特定步驟，請參閱[使用腳本操作的基於 Linux 的自訂 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)中的以下部分：

- [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
