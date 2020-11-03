---
title: InvalidNetworkSecurityGroupSecurityRules 錯誤-Azure HDInsight
description: 叢集建立失敗，並出現 ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289097"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>案例： InvalidNetworkSecurityGroupSecurityRules-叢集建立失敗 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

您會收到錯誤碼 `InvalidNetworkSecurityGroupSecurityRules` ，其描述類似于「以子網設定的網路安全性群組中的安全性規則不允許必要的輸入和/或輸出連線能力」。

## <a name="cause"></a>原因

可能是針對您的叢集設定的輸入 [網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 規則發生問題。

## <a name="resolution"></a>解決方法

移至 Azure 入口網站並識別與部署叢集的子網相關聯的 NSG。 在 [ **輸入安全性規則** ] 區段中，確定規則允許針對 [此處](../control-network-traffic.md)所述的 IP 位址，對埠443進行輸入存取。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]