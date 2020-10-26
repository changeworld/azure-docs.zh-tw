---
title: 啟動 Azure HDInsight 中的服務時發生埠衝突
description: 與 Azure HDInsight 叢集互動時，埠衝突問題的疑難排解步驟和可能的解決方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: c07cddb0999e6f3424527828be4b10ed0168a2ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533320"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>案例：啟動 Azure HDInsight 中的服務時發生埠衝突

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

服務無法啟動。

## <a name="cause"></a>原因

埠衝突存在。

## <a name="resolution"></a>解決方案

### <a name="method-1"></a>方法 1

使用下列命令來取得/終止所有執行中的進程，而這些進程會受到埠問題的影響。

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

然後啟動服務。

### <a name="method-2"></a>方法 2

重新開機節點。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。