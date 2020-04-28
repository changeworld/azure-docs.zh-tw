---
title: 在 Azure API 管理中布建自我裝載閘道 |Microsoft Docs
description: 瞭解如何在 Azure API 管理中布建自我裝載的閘道。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075278"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中布建自我裝載閘道

在您的 Azure API 管理實例中布建閘道資源是部署自我裝載閘道的先決條件。 本文會逐步解說在 API 管理中布建閘道資源的步驟。

> [!NOTE]
> 自我裝載閘道功能目前為預覽狀態。 在預覽期間，自我裝載閘道僅適用于開發人員和進階層，不需要額外付費。 開發人員層僅限於單一自我裝載的閘道部署。

## <a name="prerequisites"></a>Prerequisites

完成下列快速入門：[建立 AZURE API 管理實例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>佈建自我裝載閘道

1. 從 [**設定**] 底下選取**閘道**。
2. 按一下 [+ 新增]  。
3. 輸入閘道的 [**名稱**] 和 [**區域**]。
> [!TIP]
> **區域**指定將與此閘道資源相關聯之閘道節點的預定位置。 它在語義上相當於與任何 Azure 資源相關聯的類似屬性，但可以指派任一字元串值。

4. （選擇性）輸入閘道資源的**描述**。
5. （選擇性） **+** 選取 [在**api**底下]，將一或多個 api 與此閘道資源建立關聯。
> [!TIP]
> 您可以在 API 的 [**設定**] 索引標籤上，將 api 關聯至閘道並加以移除。

> [!IMPORTANT]
> 根據預設，不會有任何現有的 Api 與新的閘道資源相關聯。 因此，會透過新的閘道嘗試叫用`404 Resource Not Found`它們，將會產生回應。

6. 按一下 **[新增]** 。

閘道資源現在已布建在您的 API 管理實例中。 您可以繼續部署閘道。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 深入瞭解如何將[自我裝載閘道部署至 Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* 深入瞭解如何將[自我裝載閘道部署至 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
