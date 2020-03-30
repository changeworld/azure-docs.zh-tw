---
title: 在 Azure API 管理中預配自託管閘道 |微軟文檔
description: 瞭解如何在 Azure API 管理中預配自託管閘道。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075278"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>在 Azure API 管理中預配自託管閘道

在 Azure API 管理實例中預配閘道資源是部署自託管閘道的先決條件。 本文介紹在 API 管理中預配閘道資源的步驟。

> [!NOTE]
> 自託管閘道功能處於預覽狀態。 在預覽期間，自託管閘道僅在開發人員和高級層中可用，無需支付額外費用。 開發人員層僅限於單個自託管閘道部署。

## <a name="prerequisites"></a>Prerequisites

完成以下快速入門：[創建 Azure API 管理實例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>佈建自我裝載閘道

1. 從 **"設置"** 下選擇**閘道**。
2. 按一下 **" 添加**"。
3. 輸入閘道**的名稱****和區域**。
> [!TIP]
> **區域**指定將與此閘道資源關聯的閘道節點的預期位置。 它在語義上等效于與任何 Azure 資源關聯的類似屬性，但可以分配任一字元串值。

4. 可選，輸入閘道資源**的描述**。
5. 可以選擇**+** 在**API**下將一個或多個 API 與此閘道資源相關聯。
> [!TIP]
> 您可以在 API 的 **"設置"** 選項卡上從閘道關聯和刪除 API。

> [!IMPORTANT]
> 預設情況下，任何現有 API 都不會與新閘道資源關聯。 因此，嘗試通過新閘道調用它們將導致`404 Resource Not Found`回應。

6. 按一下 **[新增]**。

現在，閘道資源已在 API 管理實例中預配。 您可以繼續部署閘道。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關自託管閘道的詳細資訊，請參閱 Azure [API 管理自託管閘道概述](self-hosted-gateway-overview.md)
* 詳細瞭解如何[將自託管閘道部署到庫伯內特斯](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* 詳細瞭解如何[將自託管閘道部署到 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
