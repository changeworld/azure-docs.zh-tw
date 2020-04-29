---
title: Azure HDInsight 中的 Apache Ambari 過時警示
description: 針對 HDInsight 中 Apache Ambari 過時警示的可能原因和解決方案進行討論和分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77539105"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Ambari 過時警示

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

在 Apache Ambari UI 中，您可能會看到類似如下的警示：

![Apache Ambari 過時警示範例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

Ambari 代理程式會持續監視許多資源的健全狀況。 您可以設定*警示*，以通知您特定叢集屬性是否在預先決定的閾值內。 在執行每個資源檢查之後，如果符合警示條件，Ambari 代理程式就會將狀態回報給 Ambari 伺服器並觸發警示。 如果警示未根據其警示設定檔中的間隔進行檢查，伺服器就會觸發*Ambari 伺服器過時警示*警示。

有各種原因會導致健康情況檢查無法在定義的間隔執行：

* 主機使用量過大（高 CPU 使用率），因此 Ambari 代理程式無法取得足夠的系統資源來及時執行警示。

* 在負載過重的期間，叢集正忙於執行許多作業或服務。

* 叢集中的少數主機裝載許多元件，因此需要執行許多警示。 如果元件的數目很大，警示作業可能會錯過其排程的間隔時間。

## <a name="resolution"></a>解決方案

請嘗試下列方法來解決 Ambari 過時警示的問題。

### <a name="increase-the-alert-interval-time"></a>增加警示間隔時間

您可以根據叢集的回應時間和負載，增加個別警示間隔的值：

1. 在 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 選取您想要的警示定義名稱。
1. 從定義中，選取 [**編輯**]。
1. 增加 [**檢查間隔**] 值，然後選取 [**儲存**]。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 伺服器警示的警示間隔時間

1. 在 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 從 [**群組**] 下拉式清單中，選取 [ **AMBARI 預設值**]。
1. 選取 [ **Ambari 伺服器警示**] 警示。
1. 從定義中，選取 [**編輯**]。
1. 增加 [**檢查間隔**] 值。
1. 增加**間隔乘數**值，然後選取 [**儲存**]。

### <a name="disable-and-reenable-the-alert"></a>停用並重新啟用警示

若要捨棄過時警示，請先停用再重新啟用它：

1. 在 Apache Ambari UI 中，選取 [**警示**] 索引標籤。
1. 選取您想要的警示定義名稱。
1. 從定義中，選取 UI 最右側的 [**已啟用**]。
1. 在**確認**快顯視窗中，選取 [確認] [**停**用]。
1. 請等候幾秒鐘，讓頁面上顯示的所有「實例」警示都要清除。
1. 從定義中，選取 UI 最右側部分的 [**停用**]。
1. 在**確認**快顯視窗中，選取 [確認] [**啟用**]。

### <a name="increase-the-alert-grace-period"></a>增加警示寬限期

在 Ambari 代理程式報告已設定的警示錯過其排程之前，會有寬限期。 如果警示錯過其排定的時間，但在寬限期內執行，則不會產生過時的警示。

預設`alert_grace_period`值為5秒。 您可以在/etc/ambari-agent/conf/ambari-agent.ini. 中進行這項設定。 若是定期發生過時警示的主機，請嘗試將值增加至10。 然後，重新開機 Ambari 代理程式。

## <a name="next-steps"></a>後續步驟

如果此處未提及您的問題，或您無法解決問題，請流覽下列其中一個通道以取得更多支援：

* 在[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 在 Twitter [@AzureSupport](https://twitter.com/azuresupport)上使用連接。 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 它會將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，請從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 若要取得此資訊，請從入口網站功能表選取 [說明] （**？**），或開啟 [說明 **+ 支援**] 窗格。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 

  您的 Microsoft Azure 訂用帳戶中包含訂用帳戶管理和計費的支援。 技術支援可透過[Azure 支援方案](https://azure.microsoft.com/support/plans/)取得。
