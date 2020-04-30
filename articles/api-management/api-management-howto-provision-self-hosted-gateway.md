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
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 2870a654faad4e760a9d022488cb2c4c406cbeab
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203126"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中布建自我裝載閘道

在您的 Azure API 管理實例中布建閘道資源是部署自我裝載閘道的先決條件。 本文會逐步解說在 API 管理中布建閘道資源的步驟。

## <a name="prerequisites"></a>先決條件

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
> [!IMPORTANT]
> 根據預設，不會有任何現有的 Api 與新的閘道資源相關聯。 因此，會透過新的閘道嘗試叫用`404 Resource Not Found`它們，將會產生回應。

6. 按一下 [加入]  。

閘道資源現在已布建在您的 API 管理實例中。 您可以繼續部署閘道。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 深入瞭解如何將[自我裝載閘道部署至 Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* 深入瞭解如何將[自我裝載閘道部署至 Docker](how-to-deploy-self-hosted-gateway-docker.md)
