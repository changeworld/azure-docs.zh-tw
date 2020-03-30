---
title: 無效網路安全性群組安全規則錯誤 - Azure HDInsight
description: 群集創建失敗，錯誤代碼無效網路安全性群組安全規則
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894136"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>方案：無效網路安全性群組安全規則 - 群集創建在 Azure HDInsight 中失敗

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

您會收到描述`InvalidNetworkSecurityGroupSecurityRules`類似于"配置了子網的網路安全性群組中的安全規則不允許所需的入站和/或出站連接"的錯誤代碼。

## <a name="cause"></a>原因

為群集配置的入站[網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md)規則可能有問題。

## <a name="resolution"></a>解決方案

轉到 Azure 門戶，並標識與部署群集的子閘道聯的 NSG。 在 **"入站安全規則"** 部分中，請確保規則允許[此處](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)提到的 IP 位址對埠 443 的入站訪問。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
