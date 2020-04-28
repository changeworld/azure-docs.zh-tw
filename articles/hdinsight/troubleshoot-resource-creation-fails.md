---
title: 針對 Azure HDInsight 中的資源建立失敗進行疑難排解
description: 這篇文章提供了常見的容量問題和緩和技術。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 527d2d8cb8086ed6b5e87417e2bc80dd52aa6e63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188407"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>針對 Azure HDInsight 中的資源建立失敗進行疑難排解

本文說明與 Azure HDInsight 互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>錯誤：部署會超過配額 ' 800 '

針對每個資源群組，Azure 的部署配額限制為 800 個。 每個資源群組、訂用帳戶、帳戶或其他範圍都會套用不同的配額。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署的虛擬機器所使用的核心數超過允許的數目時，會產生錯誤訊息，指出已超過配額。

若要解決此問題，請使用 [Azure 入口網站]、[CLI] 或 [PowerShell] 來刪除不再需要的部署。

如需詳細資訊，請參閱[解決資源配額的錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>錯誤：節點數上限已超過此區域中的可用核心

您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署的資源比允許的核心多，會產生錯誤訊息，指出已超過配額。

若要要求增加配額，請遵循下列步驟：

1. 移至 [ [Azure 入口網站](https://portal.azure.com)]，然後選取 [說明 **+ 支援**]。

1. 選取 [新增支援要求]****。

1. 在 [**新增支援要求**] 頁面的 [**基本**] 索引標籤上，提供下列資訊：

   * **問題類型：** 選取 **[服務與訂用帳戶限制（配額）**]。
   * **訂**用帳戶：選取您想要修改的訂用帳戶。
   * **配額類型：** 選取 [ **HDInsight**]。

如需詳細資訊，請參閱[建立支援票證來增加核心](hdinsight-capacity-planning.md#quotas)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
