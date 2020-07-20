---
title: 什麼是 Azure 應用程式閘道
description: 了解如何使用 Azure 應用程式閘道來管理應用程式的網路流量。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78302571"
---
# <a name="what-is-azure-application-gateway"></a>什麼是 Azure 應用程式閘道？

Azure 應用程式閘道是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 傳統負載平衡器會在傳輸層 (OSI 層 4 - TCP 和 UDP) 上運作，並根據來源 IP 位址和連接埠，將流量路由傳送到目的地 IP 位址和連接埠。

應用程式閘道可讓您根據 HTTP 要求的其他屬性 (例如 URI 路徑或主機標頭) 進行路由決策。 例如，您可以根據傳入 URL 路由傳送流量。 因此，如果 `/images` 在傳入的 URL 中，您可以將流量路由傳送至一組針對影像設定的特定伺服器 (也稱為集區)。 如果 `/video` 在此 URL 中，該流量就會路由至另一個針對影片最佳化的集區。

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

這類型的路由也稱為應用程式層 (OSI 層 7) 負載平衡。 Azure 應用程式閘道可以進行 URL 型路由等作業。

>[!NOTE]
> Azure 會為您的案例提供一套完整受控的負載平衡解決方案。 如果您需要高效能、低延遲、Layer 4 負載平衡，請參閱[什麼是 Azure Load Balancer？](../load-balancer/load-balancer-overview.md) 如果您要尋找全域 DNS 負載平衡，請參閱[什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md) 結合這些解決方案，可能有益於您的端對端案例。
>
> 如需 Azure 負載平衡選項的比較，請參閱 [Azure 中的負載平衡選項概觀](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)。

## <a name="features"></a>特性

若要了解「應用程式閘道」功能，請參閱 [Azure 應用程式閘道功能](features.md)。

## <a name="pricing-and-sla"></a>價格和 SLA

如需應用程式閘道定價資訊，請參閱[應用程式閘道定價](https://azure.microsoft.com/pricing/details/application-gateway/)。

如需應用程式閘道 SLA 資訊，請參閱[應用程式閘道 SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)。

## <a name="next-steps"></a>後續步驟

視需求和環境而定，您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來建立測試應用程式閘道。

- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure 入口網站](quick-create-portal.md)
- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure PowerShell](quick-create-powershell.md)
- [快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure CLI](quick-create-cli.md)
