---
title: 叢集建立失敗，但 Azure HDInsight 發生 DomainNotFound 錯誤
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005940"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>案例：叢集建立失敗，但 Azure HDInsight 發生 DomainNotFound 錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

HDI 安全 (企業安全性套件) 叢集建立失敗，並出現 `DomainNotFound` 錯誤訊息。

## <a name="cause"></a>原因

DNS 設定不正確。

## <a name="resolution"></a>解決方法

部署已加入網域的叢集時，HDI 會在 AAD DS (中建立每個叢集) 的內部使用者名稱和密碼，並將所有叢集節點加入此網域。 使用 Samba 工具即可完成網域加入。 請確保已符合下列必要條件︰

* 功能變數名稱應透過 DNS 解析。
* 您應該在部署叢集的虛擬網路的 DNS 設定中設定網域控制站的 IP 位址。
* 如果虛擬網路是使用 AAD DS 的虛擬網路所對等互連，則必須手動完成。
* 如果您使用的是 DNS 轉寄站，則必須在虛擬網路內正確解析功能變數名稱。
*  (Nsg 的安全性原則) 不應封鎖加入網域。

### <a name="additional-debugging-steps"></a>其他的調試步驟

* 將 windows VM 部署在相同的子網中，使用使用者名稱和密碼將電腦加入網域 (這可透過 [控制台] UI) 來完成，或

* 在相同的子網中部署 ubuntu VM，並將電腦加入網域
  * 透過 SSH 連線到電腦
  * sudo su
  * 以使用者名稱和密碼執行腳本
  * 腳本會偵測到，然後建立必要的設定檔，再建立網域。 如果成功，則您的 DNS 設定是良好的。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]