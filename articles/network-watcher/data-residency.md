---
title: 適用于 Azure 網路監看員的資料存放區 |Microsoft Docs
description: 本文將協助您瞭解 Azure 網路監看員服務的資料存放區。
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
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682956"
---
# <a name="data-residency-for-azure-network-watcher"></a>適用于 Azure 網路監看員的資料存放區
除了連線監視器 (Preview) 服務之外，Azure 網路監看員不會儲存客戶資料。


## <a name="connection-monitor-preview-data-residency"></a>連接監視器 (預覽版) 資料存放區
連線監視器 (預覽版) 服務會儲存客戶資料。 網路監看員會自動將此資料儲存在單一區域中。 因此，連線監視器 (預覽) 會自動滿足區域內資料落地需求，包括 [信任中心](https://azuredatacentermap.azurewebsites.net/)所指定的需求。

## <a name="data-residency"></a>資料存留處
在 Azure 中，可在單一區域中儲存客戶資料的功能，目前僅適用于東南亞地區 (新加坡) 在巴西地區的亞太地區地區和巴西南部 (聖保羅州) 地區。 至於其他所有區域，客戶資料會儲存在地區中。 如需詳細資訊，請參閱「 [信任中心](https://azuredatacentermap.azurewebsites.net/)」。

## <a name="next-steps"></a>後續步驟

* 閱讀 [網路](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)監看員的總覽。
