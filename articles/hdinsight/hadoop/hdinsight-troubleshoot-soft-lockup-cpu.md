---
title: Azure HDInsight 叢集的看門狗 BUG 軟鎖定 CPU 錯誤
description: Azure HDInsight 叢集的核心 syslog 中出現看門狗 BUG 軟鎖定 CPU
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894113"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>案例： "看門狗： BUG：虛鎖定-CPU" 來自 Azure HDInsight 叢集的錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

核心 syslog 包含錯誤訊息： `watchdog: BUG: soft lockup - CPU` 。

## <a name="cause"></a>原因

Linux 核心中的 [錯誤](https://bugzilla.kernel.org/show_bug.cgi?id=199437) 導致 CPU 虛鎖定。

## <a name="resolution"></a>解決方案

套用核心修補程式。 下列腳本會升級 linux 核心，並在24小時內于不同的時間重新開機電腦。 以兩個批次執行腳本動作。 第一個批次位於前端節點以外的所有節點上。 第二個批次位於前端節點上。 請勿同時在前端節點和其他節點上執行。

1. 從 Azure 入口網站流覽至您的 HDInsight 叢集。

1. 移至腳本動作。

1. 選取 [ **提交新** 的] 並輸入輸入，如下所示

    | 屬性 | 值 |
    | --- | --- |
    | 指令碼類型 | -自訂 |
    | 名稱 |核心軟鎖定問題的修正 |
    | Bash 指令碼 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 節點類型 |Worker、Zookeeper |
    | 參數 |N/A |

    如果您想要在加入新節點時執行腳本，請選取 [ **保存此腳本動作** ]。

1. 選取 [建立]  。

1. 等候執行成功。

1. 遵循步驟3中的相同步驟，在前端節點上執行腳本動作，但這次使用節點類型： Head。

1. 等候執行成功。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
