---
title: Azure HDInsight 中的 Apache Ambari 過時警示
description: 針對 HDInsight 中過時 Apache Ambari 警示的可能原因和解決方案進行討論和分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722807"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Ambari 過時警示

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

在 Apache Ambari UI 中，您可能會看到類似下圖的警示：

![Apache Ambari 過時警示範例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

Ambari 代理程式會持續執行健康情況檢查，以監視許多資源的健全狀況。 每個警示都設定為在預先定義的時間間隔執行。 執行每個警示之後，Ambari 代理程式會向 Ambari 伺服器報告狀態。 此時，如果 Ambari 伺服器偵測到任何警示都未及時執行，則會觸發「Ambari 伺服器警示」。 健康狀態檢查可能無法在定義的間隔執行的原因有很多種：

* 當主機耗用大量使用率（高 CPU）時，Ambari 代理程式可能無法及時取得足夠的系統資源來即時執行警示。

* 在大量載入期間，叢集正忙於執行許多作業/服務。

* 叢集中的少數主機可能裝載許多元件，因此需要執行許多警示。 如果元件的數目很大，警示工作可能會錯過排程的間隔

## <a name="resolution"></a>解決方案

### <a name="increase-alert-interval-time"></a>增加警示間隔時間

您可以根據叢集的回應時間和負載，選擇增加個別警示間隔的值。

1. 從 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 選取所需的警示定義名稱。
1. 從定義中，選取 [**編輯**]。
1. 視需要修改 [**檢查間隔**] 值，然後選取 [**儲存**]。

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 伺服器警示的警示間隔時間

1. 從 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 從 [**群組**] 下拉式清單中，選取 [ **AMBARI 預設值**]。
1. 選取 [警示**Ambari 伺服器警示**]。
1. 從定義中，選取 [**編輯**]。
1. 視需要修改**檢查間隔**值。
1. 視需要修改**間隔乘數**值，然後選取 [**儲存**]。

### <a name="disable-and-enable-the-alert"></a>停用並啟用警示

您可以停用再重新啟用警示，以捨棄任何過時的警示。

1. 從 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 選取所需的警示定義名稱。
1. 從定義中，選取位於最右側的 [**已啟用**]。
1. 從**確認**快顯視窗中，選取 [**確認**] [停用]。
1. 請等候幾秒鐘，讓頁面上顯示的所有「實例」警示都已清除。
1. 從定義中，選取位於最右側的 [**停用**]。
1. 從**確認**快顯視窗中，選取 [**確認**] [啟用]。

### <a name="increase-alert-grace-time"></a>增加警示寬限期

在 Ambari 代理程式報告已設定的警示錯過其排程之前，會套用寬限期。 即使警示錯過其排定的時間，但在警示寬限期內觸發，也不會引發過時警示。

預設的 `alert_grace_period` 值為5秒。 此 `alert_grace_period` 設定可在 `/etc/ambari-agent/conf/ambari-agent.ini`中進行設定。 若為定期引發過時警示的主機，請嘗試增加為10的值。 然後重新開機 Ambari 代理程式

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
