---
title: 移動 Azure 網路監看員資源 |Microsoft Docs
description: 跨區域移動 Azure 網路監看員資源
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019647"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>跨區域移動 Azure 網路監看員資源

## <a name="moving-the-network-watcher-resource"></a>移動網路監看員資源
網路監看員資源代表網路監看員的後端服務，完全由 Azure 管理。 客戶不需要管理它。 此資源不支援移動作業。

## <a name="moving-child-resources-of-network-watcher"></a>移動網路監看員的子資源
資源類型的任何子資源目前不支援跨區域移動資源 `*networkWatcher*` 。

## <a name="next-steps"></a>後續步驟
* 閱讀網路監看員 [總覽](./network-watcher-monitoring-overview.md)
* 查看網路監看員 [常見問題](./frequently-asked-questions.md)