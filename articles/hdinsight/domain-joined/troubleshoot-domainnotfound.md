---
title: 叢集建立失敗，發生 DomainNotFound 錯誤，Azure HDInsight
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76776231"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>案例：叢集建立失敗，併發生 DomainNotFound 錯誤 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

HDI Secure （企業安全性套件）叢集建立失敗，並出現 `DomainNotFound` 錯誤訊息。

## <a name="cause"></a>原因

DNS 設定不正確。

## <a name="resolution"></a>解決方案

部署加入網域的叢集時，HDI 會在 AAD DS （針對每個叢集）中建立內部使用者名稱和密碼，並將所有叢集節點聯結至此網域。 加入網域的作業是使用 Samba 工具來完成。 請確保已符合下列必要條件︰

* 功能變數名稱應透過 DNS 解析。
* 網域控制站的 IP 位址應設定于部署叢集之虛擬網路的 DNS 設定中。
* 如果虛擬網路與 AAD DS 的虛擬網路對等互連，則必須手動進行。
* 如果您使用 DNS 轉寄站，則功能變數名稱必須在虛擬網路內正確解析。
* 安全性原則（Nsg）不應封鎖加入網域。

### <a name="additional-debugging-steps"></a>其他調試步驟

* 將 windows VM 部署在相同的子網中，使用使用者名稱和密碼將電腦加入網域（這可以透過 [控制台] UI 來完成），或

* 在相同的子網中部署 ubuntu VM，並將電腦加入網域
  * 透過 SSH 連線到電腦
  * sudo su
  * 以使用者名稱和密碼執行腳本
  * 腳本會進行 ping，建立必要的設定檔，然後再建立網域。 如果成功，您的 DNS 設定就很好。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
