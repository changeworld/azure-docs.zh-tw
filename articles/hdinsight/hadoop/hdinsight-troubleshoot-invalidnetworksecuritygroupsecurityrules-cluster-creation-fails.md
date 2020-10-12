---
title: InvalidNetworkSecurityGroupSecurityRules 錯誤-Azure HDInsight
description: 叢集建立失敗，並出現 ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2cf4859d3bf4c34fff4cb076eec11bcd2d81e4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82780771"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>案例： InvalidNetworkSecurityGroupSecurityRules-叢集建立失敗 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

您會收到錯誤碼 `InvalidNetworkSecurityGroupSecurityRules` ，其描述類似于「以子網設定的網路安全性群組中的安全性規則不允許必要的輸入和/或輸出連線能力」。

## <a name="cause"></a>原因

可能是針對您的叢集設定的輸入 [網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 規則發生問題。

## <a name="resolution"></a>解決方案

移至 Azure 入口網站並識別與部署叢集的子網相關聯的 NSG。 在 [ **輸入安全性規則** ] 區段中，確定規則允許針對 [此處](../control-network-traffic.md)所述的 IP 位址，對埠443進行輸入存取。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
