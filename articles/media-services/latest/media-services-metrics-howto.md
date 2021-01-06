---
title: 使用 Azure 監視器來查看度量
description: 本文說明如何使用 Azure 入口網站圖和 Azure CLI 來監視計量。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 91e74668224d1cdbbc462bf6c2c0a22c4f0a9837
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916570"
---
# <a name="monitor-media-services-metrics"></a>監視媒體服務計量

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure 監視器](../../azure-monitor/overview.md) 可讓您監視計量和診斷記錄，以協助您瞭解應用程式的執行情況。 如需這項功能的詳細說明，以及瞭解為何應該使用 Azure 媒體服務計量和診斷記錄，請參閱 [監視媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

Azure 監視器提供數種方式來與計量互動，包括在入口網站中製作圖表、透過 REST API 存取它們，或使用 Azure CLI 查詢它們。 本文說明如何使用 Azure 入口網站圖和 Azure CLI 來監視計量。

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](./create-account-howto.md)
- 查看  [監視器媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>查看 Azure 入口網站中的計量

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 流覽至您的 Azure 媒體服務帳戶，然後選取 [ **計量**]。
1. 按一下 [ **領域** ] 方塊，然後選取您想要監視的資源。

    [ **選取領域** ] 視窗會出現在右側，其中有可用的資源清單。 在此情況下，您會看到：

    * &lt;媒體服務帳戶名稱&gt;
    * &lt;媒體服務帳戶名稱 &gt; / &lt; 串流端點名稱&gt;
    * &lt;儲存體帳戶名稱&gt;

    篩選準則，然後選取資源， **再按 [** 套用]。 如需支援的資源和度量的詳細資訊，請參閱 [監視媒體服務計量](media-services-metrics-diagnostic-logs.md)。

    > [!NOTE]
    > 若要在您要監視的資源之間切換，請再次按一下 [ **來源** ] 方塊，然後重複此步驟。

1. 選擇性：將您的圖表命名為 (編輯名稱，方法是按頂端) 的鉛筆。
1. 新增您想要查看的度量。
1. 您可以將圖表釘選到儀表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 來查看度量

若要使用 Azure CLI 取得「輸出」計量，請執行下列 `az monitor metrics` 命令：

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

若要取得其他計量，請以「輸出」取代您感興趣的度量名稱。

## <a name="see-also"></a>另請參閱

- [Azure 監視器計量](../../azure-monitor/platform/data-platform.md)
- [使用 Azure 監視器建立、查看和管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>後續步驟

[診斷記錄](media-services-diagnostic-logs-howto.md)
