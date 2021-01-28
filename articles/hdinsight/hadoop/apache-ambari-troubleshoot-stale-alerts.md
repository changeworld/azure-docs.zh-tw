---
title: Apache Ambari Azure HDInsight 中的過時警示
description: 適用于 HDInsight 中 Apache Ambari 過時警示的可能原因和解決方案的討論與分析。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: e31e5d5a5b27f4dcb267905eae6329666fc3292c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946714"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>案例： Apache Ambari Azure HDInsight 中的過時警示

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

在 Apache Ambari UI 中，您可能會看到如下所示的警示：

![Apache Ambari 過時警示範例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

Ambari 代理程式會持續監視許多資源的健全狀況。 您可以設定 *警示*，以通知您特定叢集屬性是否在預先決定的閾值內。 每次執行資源檢查之後，如果符合警示條件，Ambari 代理程式就會將狀態回報給 Ambari 伺服器，並觸發警示。 如果未根據警示設定檔中的間隔檢查警示，伺服器會觸發 *Ambari 伺服器過時警示* 警示。

健康情況檢查在定義的間隔內可能無法執行的原因有很多：

* 主機的使用量很高， (高 CPU 使用率) ，因此 Ambari 代理程式無法取得足夠的系統資源來及時執行警示。

* 叢集在大量載入期間執行許多作業或服務時忙碌。

* 叢集中的少數主機裝載許多元件，因此必須執行許多警示。 如果元件數目很大，警示工作可能會錯過其排程間隔。

## <a name="resolution"></a>解決方案

請嘗試下列方法來解決 Ambari 過時警示的問題。

### <a name="increase-the-alert-interval-time"></a>增加警示間隔時間

您可以根據叢集的回應時間和負載，增加個別警示間隔的值：

1. 在 Apache Ambari UI 中，選取 [ **警示** ] 索引標籤。
1. 選取您要的警示定義名稱。
1. 從定義中選取 [ **編輯**]。
1. 增加 [ **檢查間隔** ] 值，然後選取 [ **儲存**]。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 伺服器警示的警示間隔時間

1. 在 Apache Ambari UI 中，選取 [ **警示** ] 索引標籤。
1. 從 [ **群組** ] 下拉式清單中，選取 [ **AMBARI 預設值**]。
1. 選取 [ **Ambari 伺服器警示** ] 警示。
1. 從定義中選取 [ **編輯**]。
1. 增加 **檢查間隔** 值。
1. 增加 **間隔乘數** 值，然後選取 [ **儲存**]。

### <a name="disable-and-reenable-the-alert"></a>停用並重新啟用警示

若要捨棄過時的警示，請停用再重新啟用它：

1. 在 Apache Ambari UI 中，選取 [ **警示** ] 索引標籤。
1. 選取您要的警示定義名稱。
1. 從定義中選取 UI 最右側的 [ **已啟用** ]。
1. 在 **確認** 的快顯視窗中，選取 [ **確認停** 用]。
1. 請等候幾秒鐘，讓頁面上顯示的所有警示「實例」清除。
1. 從定義中，選取 UI 最右側部分的 [ **已停用** ]。
1. 在 **確認** 的快顯視窗中，選取 [ **確認啟用**]。

### <a name="increase-the-alert-grace-period"></a>增加警示寬限期

Ambari 代理程式回報已設定的警示缺少其排程之前，會有寬限期。 如果警示錯過其排程時間，但在寬限期內執行，則不會產生過時的警示。

預設 `alert_grace_period` 值為5秒。 您可以在/etc/ambari-agent/conf/ambari-agent.ini 中設定這項設定。 針對過時警示定期發生的主機，請嘗試將此值增加為10。 然後，重新開機 Ambari 代理程式。

## <a name="next-steps"></a>後續步驟

如果此處未提及您的問題，或您無法解決問題，請流覽下列其中一個通道以取得更多支援：

* 在 azure [群體支援](https://azure.microsoft.com/support/community/)中取得 azure 專家提供的解答。

* [@AzureSupport](https://twitter.com/azuresupport)在 Twitter 上連接。 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 它會將 Azure 群體連接到正確的資源：解答、支援及專家。

* 如果您需要更多協助，請從 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 若要取得，請從入口網站功能表選取 [說明 (**？**) ，或開啟 [說明 **+ 支援** ] 窗格。 如需詳細資訊，請參閱 [如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 

  訂用帳戶管理和帳單的支援隨附于您的 Microsoft Azure 訂用帳戶。 您可以透過 [Azure 支援方案](https://azure.microsoft.com/support/plans/)取得技術支援。