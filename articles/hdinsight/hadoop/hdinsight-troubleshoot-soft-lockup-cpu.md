---
title: Azure HDInsight 叢集的看門狗 BUG 軟鎖定 CPU 錯誤
description: 監視 BUG 的軟鎖定 CPU 會出現在 Azure HDInsight 叢集的核心 syslog 中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894113"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>案例：來自 Azure HDInsight 叢集的「監視程式： BUG：軟鎖定-CPU」錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

核心 syslog 包含錯誤訊息： `watchdog: BUG: soft lockup - CPU`。

## <a name="cause"></a>原因

Linux 核心中的[bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437)導致 CPU 軟鎖定。

## <a name="resolution"></a>解決方法

套用核心修補程式。 下列腳本會升級 linux 核心，並在24小時內于不同時間重新開機電腦。 以兩個批次執行腳本動作。 第一個批次位於前端節點以外的所有節點上。 第二個批次位於前端節點上。 請勿同時在前端節點和其他節點上執行。

1. 從 Azure 入口網站流覽至您的 HDInsight 叢集。

1. 移至 [腳本動作]。

1. 選取 [**提交新**的]，並依照下列方式輸入輸入：

    | 屬性 | 值 |
    | --- | --- |
    | 指令碼類型 | -Custom |
    | Name |核心軟鎖定問題的修正 |
    | Bash 指令碼 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 節點類型 |Worker、Zookeeper |
    | 參數 |N/A |

    如果您想要在新增節點時執行腳本，請選取 [**保存此腳本動作 ...** ]。

1. 選取 [建立]  。

1. 等待執行成功。

1. 遵循步驟3的相同步驟，在前端節點上執行腳本動作，但這次使用節點類型： Head。

1. 等待執行成功。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
