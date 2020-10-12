---
title: 針對 Azure HDInsight 中的資源建立失敗進行疑難排解
description: 本文提供常見的容量問題錯誤和緩和技術。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 527d2d8cb8086ed6b5e87417e2bc80dd52aa6e63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82188407"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>針對 Azure HDInsight 中的資源建立失敗進行疑難排解

本文說明與 Azure HDInsight 互動時問題的疑難排解步驟和可能的解決方法。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>錯誤：部署會超過配額 ' 800 '

針對每個資源群組，Azure 的部署配額限制為 800 個。 每個資源群組、訂用帳戶、帳戶或其他範圍都會套用不同的配額。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署的虛擬機器所含的核心數目超過允許的結果，會出現錯誤訊息，指出已超過配額。

若要解決此問題，請使用 Azure 入口網站、CLI 或 PowerShell 來刪除不再需要的部署。

如需詳細資訊，請參閱[解決資源配額的錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>錯誤：節點數上限超過此區域可用的核心數目

您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署具有比允許的核心更多的資源時，會出現錯誤訊息，指出已超過配額。

若要要求增加配額，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)，然後選取 [說明 **+ 支援**]。

1. 選取 [新增支援要求]。

1. 在 [**新增支援要求**] 頁面的 [**基本**] 索引標籤上，提供下列資訊：

   * **問題類型：** 選取 ** (配額) 的服務和訂 **用帳戶限制。
   * **訂** 用帳戶：選取您要修改的訂用帳戶。
   * **配額類型：** 選取 [ **HDInsight**]。

如需詳細資訊，請參閱[建立支援票證來增加核心](hdinsight-capacity-planning.md#quotas)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
