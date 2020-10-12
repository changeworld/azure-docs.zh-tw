---
title: Azure 應用程式閘道設定總覽
description: 本文說明如何設定 Azure 應用程式閘道的元件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651997"
---
# <a name="application-gateway-configuration-overview"></a>應用程式閘道設定總覽

Azure 應用程式閘道包含數個元件，您可以在不同的案例中以各種方式設定這些元件。 本文說明如何設定每個元件。

![應用程式閘道元件流程圖](./media/configuration-overview/configuration-overview1.png)

此圖說明有三個接聽程式的應用程式。 前兩個分別是和的多網站接聽程式 `http://acme.com/*` `http://fabrikam.com/*` 。 兩者都在埠80上接聽。 第三個是具有端對端傳輸層安全性 (TLS) 終止的基本接聽程式，先前稱為安全通訊端層 (SSL) 終止。

## <a name="infrastructure"></a>基礎結構

應用程式閘道基礎結構包括虛擬網路、子網、網路安全性群組和使用者定義的路由。

如需詳細資訊，請參閱 [應用程式閘道基礎結構](configuration-infrastructure.md)設定。



## <a name="front-end-ip-address"></a>前端 IP 位址

您可以將應用程式閘道設定為具有公用 IP 位址、私人 IP 位址或兩者。 當您裝載後端，用戶端必須透過網際網路對向的虛擬 IP (VIP) 來透過網際網路存取時，就需要公用 IP。

如需詳細資訊，請參閱 [應用程式閘道前端 IP 位址](configuration-front-end-ip.md)設定。

## <a name="listeners"></a>接聽程式

接聽程式是一種邏輯實體，會使用埠、通訊協定、主機和 IP 位址來檢查連入連線要求。 當您設定接聽程式時，必須輸入符合閘道傳入要求中對應值的值。

如需詳細資訊，請參閱 [應用程式閘道接聽程式](configuration-listeners.md)設定。

## <a name="request-routing-rules"></a>要求路由規則

當您使用 Azure 入口網站建立應用程式閘道時，您會 (*rule1*) 建立預設規則。 此規則會將預設的接聽程式 (*appGatewayHttpListener*) 與預設的後端集區系結 (*appGatewayBackendPool*) 和預設的後端 HTTP 設定 (*appGatewayBackendHttpSettings*) 。 建立閘道之後，您可以編輯預設規則的設定，或建立新的規則。

如需詳細資訊，請參閱 [應用程式閘道要求路由規則](configuration-request-routing-rules.md)。

## <a name="http-settings"></a>HTTP 設定

應用程式閘道會使用您在此處指定的設定，將流量路由傳送至後端伺服器。 建立 HTTP 設定之後，您必須將它與一或多個要求路由規則相關聯。

如需詳細資訊，請參閱 [應用程式閘道 HTTP 設定](configuration-http-settings.md)。

## <a name="back-end-pool"></a>後端集區

您可以將後端集區指向四種類型的後端成員：特定虛擬機器、虛擬機器擴展集、IP 位址/FQDN 或 app service。 

建立後端集區之後，您必須將它與一或多個要求路由規則建立關聯。 您也必須針對應用程式閘道上的每個後端集區設定健康情況探查。 當符合要求路由規則條件時，應用程式閘道會將流量轉送到狀況良好的伺服器 (由對應後端集區中的健康情況探查) 所決定。

## <a name="health-probes"></a>健康狀態探查

應用程式閘道預設會監視其後端中所有資源的健康情況。 但強烈建議您為每個後端 HTTP 設定建立自訂探查，以更充分掌控健康情況監視。 若要瞭解如何設定自訂探查，請參閱 [自訂健康情況探查設定](application-gateway-probe-overview.md#custom-health-probe-settings)。

> [!NOTE]
> 建立自訂健康情況探查之後，您必須將它與後端 HTTP 設定產生關聯。 除非對應的 HTTP 設定與使用規則的接聽程式明確相關聯，否則自訂探查不會監視後端集區的健康情況。

## <a name="next-steps"></a>後續步驟

現在您已瞭解應用程式閘道元件，您可以：

- [在 Azure 入口網站中建立應用程式閘道](quick-create-portal.md)
- [使用 PowerShell 建立應用程式閘道](quick-create-powershell.md)
- [使用 Azure CLI 建立應用程式閘道](quick-create-cli.md)
