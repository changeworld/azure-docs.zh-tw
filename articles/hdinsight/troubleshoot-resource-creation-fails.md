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
ms.openlocfilehash: 2e9f6898dba559f557f49cc0ee9ff39644da243f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287349"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>針對 Azure HDInsight 中的資源建立失敗進行疑難排解

本文說明與 Azure HDInsight 互動時問題的疑難排解步驟和可能的解決方法。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>錯誤：部署會超過配額 ' 800 '

針對每個資源群組，Azure 的部署配額限制為 800 個。 每個資源群組、訂用帳戶、帳戶或其他範圍都會套用不同的配額。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署的虛擬機器所含的核心數目超過允許的結果，會出現錯誤訊息，指出已超過配額。

若要解決此問題，請使用 Azure 入口網站、CLI 或 PowerShell 來刪除不再需要的部署。

如需詳細資訊，請參閱[解決資源配額的錯誤](../azure-resource-manager/templates/error-resource-quota.md)。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>錯誤：節點數上限超過此區域可用的核心數目

您的訂用帳戶可能設定為要限制區域的核心數目。 嘗試部署具有比允許的核心更多的資源時，會出現錯誤訊息，指出已超過配額。

若要要求增加配額，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)，然後選取 [說明 **+ 支援** ]。

1. 選取 [新增支援要求]。

1. 在 [ **新增支援要求** ] 頁面的 [ **基本** ] 索引標籤上，提供下列資訊：

   * **問題類型：** 選取 **(配額) 的服務和訂** 用帳戶限制。
   * **訂** 用帳戶：選取您要修改的訂用帳戶。
   * **配額類型：** 選取 [ **HDInsight** ]。

如需詳細資訊，請參閱[建立支援票證來增加核心](hdinsight-capacity-planning.md#quotas)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]