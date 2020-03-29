---
title: 群集創建失敗，Azure HDInsight 中域未找到錯誤
description: 在與 Azure HDInsight 群集交互時，對問題進行故障排除步驟和可能的解決方案
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776231"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>方案：群集創建失敗，Azure HDInsight 中域未發現錯誤

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

HDI 安全（企業安全包）群集創建失敗`DomainNotFound`，出現錯誤訊息。

## <a name="cause"></a>原因

DNS 設置不正確。

## <a name="resolution"></a>解決方案

部署域加入群集時，HDI 會在 AAD DS 中創建內部使用者名和密碼（每個群集），並將所有叢集節點聯接到此域。 域聯接使用 Samba 工具完成。 請確保已符合下列必要條件︰

* 功能變數名稱應通過 DNS 解析。
* 網域控制站的 IP 位址應在部署群集的虛擬網路的 DNS 設置中設置。
* 如果虛擬網路與 AAD DS 的虛擬網路對等，則必須手動完成。
* 如果您使用的是 DNS 轉寄站，則功能變數名稱必須在虛擬網路中正確解析。
* 安全性原則 （NSG） 不應阻止域加入。

### <a name="additional-debugging-steps"></a>其他調試步驟

* 在同一子網中部署視窗 VM，域使用使用者名和密碼加入電腦（這可以通過控制台 UI 完成），或者

* 在同一子網中部署 ubuntu VM，並加入電腦
  * SSH 進入機器
  * sudo su
  * 使用使用者名和密碼運行腳本
  * 腳本將 ping，創建所需的設定檔，然後域。 如果成功，則 DNS 設置良好。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
