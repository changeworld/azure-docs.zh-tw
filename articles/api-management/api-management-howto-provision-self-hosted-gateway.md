---
title: 在 Azure API 管理中布建自我裝載的閘道 |Microsoft Docs
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
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050353"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中布建自我裝載的閘道

在您的 Azure API 管理實例中布建閘道資源是部署自我裝載閘道的先決條件。 本文將逐步解說在 API 管理中布建閘道資源的步驟。

## <a name="prerequisites"></a>Prerequisites

完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>佈建自我裝載閘道

1. 從 [**部署和基礎結構**] 底下選取**閘道**。
2. 按一下 [+ 新增]。
3. 輸入閘道的 **名稱** 和 **區域** 。
> [!TIP]
> **區域** 會指定將與此閘道資源相關聯之閘道節點的預定位置。 它在語義上相當於與任何 Azure 資源相關聯的類似屬性，但可以指派任一字元串值。

4. （選擇性）輸入閘道資源的 **描述** 。
5. （選擇性）在 [Api] 底下選取， **+** 以將一或多個 api 與此閘道資源建立關聯。 **APIs**
> [!IMPORTANT]
> 根據預設，任何現有的 Api 都不會與新的閘道資源相關聯。 因此，嘗試透過新的閘道叫用它們將會產生 `404 Resource Not Found` 回應。

6. 按一下 [新增] 。

閘道資源現在已布建在您的 API 管理實例中。 您可以繼續部署閘道。

## <a name="next-steps"></a>接下來的步驟

* 若要深入瞭解自我裝載的閘道，請參閱 [AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 深入瞭解如何將 [自我裝載閘道部署至 Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* 深入瞭解如何將 [自我裝載閘道部署至 Docker](how-to-deploy-self-hosted-gateway-docker.md)
