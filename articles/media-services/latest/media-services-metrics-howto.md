---
title: 使用 Azure 監視器查看指標
description: 本文演示如何使用 Azure 門戶圖表和 Azure CLI 監視指標。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382931"
---
# <a name="monitor-media-services-metrics"></a>監視媒體服務計量

[Azure 監視器](../../azure-monitor/overview.md)使您能夠監視指標和診斷日誌，以説明您瞭解應用程式的性能。 有關此功能的詳細說明，並瞭解為什麼要使用 Azure 媒體服務指標和診斷日誌，請參閱[監視媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)。

Azure 監視器提供了幾種與指標進行交互的方法，包括在門戶中繪製指標的圖表、通過 REST API 訪問指標或使用 Azure CLI 查詢指標。 本文演示如何使用 Azure 門戶圖表和 Azure CLI 監視指標。

## <a name="prerequisites"></a>Prerequisites

- [建立媒體服務帳戶](create-account-cli-how-to.md)
- 查看[監視器媒體服務指標和診斷日誌](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>在 Azure 門戶中查看指標

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 導航到 Azure 媒體服務帳戶並選擇**指標**。
1. 按一下 **"資源"** 框並選擇要監視指標的資源。

    "**選擇資源**"視窗顯示在右側，並列出可供您使用的資源。 在這種情況下，您將看到：

    * &lt;媒體服務帳戶名稱&gt;
    * &lt;媒體服務帳戶名稱&gt;/&lt;流式處理終結點名稱&gt;
    * &lt;存儲帳戶名稱&gt;

    選擇資源並按 **"應用**"。 有關受支援的資源和指標的詳細資訊，請參閱[監視媒體服務指標](media-services-metrics-diagnostic-logs.md)。

    ![計量](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > 要在要監視指標的資源之間切換，請再次按一下 **"資源"** 框並重複此步驟。
1. （可選）為您的圖表指定一個名稱（通過按下頂部的鉛筆編輯名稱）。
1. 添加要查看的指標。

    ![計量](media/media-services-metrics/metrics03.png)
1. 您可以將圖表固定到儀表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 查看指標

要使用 Azure CLI 獲取"出口"指標，將運行以下`az monitor metrics`命令：

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

要獲取其他指標，將"出口"替換為您感興趣的指標名稱。

## <a name="see-also"></a>另請參閱

* [Azure 監視器指標](../../azure-monitor/platform/data-platform.md)
* [使用 Azure 監視器 創建、查看和管理指標警報](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>後續步驟

[診斷日誌](media-services-diagnostic-logs-howto.md)
