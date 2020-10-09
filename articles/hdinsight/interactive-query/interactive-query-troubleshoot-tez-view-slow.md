---
title: Apache Ambari Tez View Azure HDInsight 載入緩慢
description: Apache Ambari Tez View 可能載入速度很慢，或根本不會載入 Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895100"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>案例： Apache Ambari Tez View Azure HDInsight 載入緩慢

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Ambari Tez View 可能載入速度很慢或根本不會載入。 載入 Ambari Tez View 時，您可能會看到前端節點上的進程變得沒有回應。

## <a name="cause"></a>原因

存取 Yarn ATS Api 有時可能會在叢集上有大量的 Hive 作業執行時，于2017年10月之前建立的叢集上發生效能不佳的情況。

## <a name="resolution"></a>解決方案

這是在2017年10月修正的問題。 重新建立叢集之後，就不會再次遇到這個問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
