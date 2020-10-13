---
title: 路由搭配 Azure Dev Spaces 的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的處理常式，以及路由的運作方式
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977964"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由搭配 Azure Dev Spaces 的運作方式

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的專案在開發人員空間中執行時，Azure Dev Spaces 為您的專案提供額外的網路功能和路由功能。

本文說明路由如何搭配 Dev Spaces 運作。

## <a name="how-routing-works"></a>路由的運作方式

開發空間建置於 AKS 之上，並使用相同的 [網路概念](../aks/concepts-network.md)。 Azure Dev Spaces 也有集中化的 *ingressmanager* 服務，並將它自己的輸入控制器部署到 AKS 叢集。 *Ingressmanager*服務會使用 dev spaces 來監視 AKS 叢集，並使用輸入物件來增強叢集中的 Azure Dev Spaces 輸入控制器，以路由傳送至應用程式 pod。 每個 pod 中的 microsoft.devspaces proxy 容器會 `azds-route-as` 根據 URL 將 HTTP 流量的 HTTP 標頭新增至開發空間。 例如，對 URL 的要求 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 會取得具有的 HTTP 標頭 `azds-route-as: azureuser` 。 Microsoft.devspaces-proxy 容器將不會新增 `azds-route-as` 標頭（如果已有的話）。

從叢集外部對服務發出 HTTP 要求時，要求會進入輸入控制器。 輸入控制器會根據輸入物件和規則，將要求直接路由傳送到適當的 pod。 Pod 中的 microsoft.devspaces proxy 容器會接收要求、 `azds-route-as` 根據 URL 新增標頭，然後將要求路由傳送至應用程式容器。

從叢集中的另一個服務對服務發出 HTTP 要求時，要求會先通過呼叫服務的 microsoft.devspaces proxy 容器。 Microsoft.devspaces proxy 容器會查看 HTTP 要求，並檢查 `azds-route-as` 標頭。 根據標頭，microsoft.devspaces proxy 容器會查詢與標頭值相關聯之服務的 IP 位址。 如果找到 IP 位址，microsoft.devspaces proxy 容器會將要求路由至該 IP 位址。 如果找不到 IP 位址，microsoft.devspaces proxy 容器會將要求路由傳送至父應用程式容器。

例如，應用程式 *services* 和 *serviceB* 會部署至名為 *default*的父開發人員空間。 *services* 依賴 *serviceB* ，並對其進行 HTTP 呼叫。 Azure 使用者會根據名為 *>azureuser*的*預設*空間建立子開發空間。 Azure 使用者也會將自己的 *services* 版本部署到其子空間。 提出要求時 *http://azureuser.s.default.serviceA.fedcba09...azds.io* ：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 輸入控制器會查閱與 URL 相關聯之 pod 的 IP，也就是 *services. >azureuser*。
1. 輸入控制器會在 Azure 使用者的開發人員空間中尋找 pod 的 IP，並將要求路由傳送至 *services. >azureuser* pod。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會收到要求，並新增 `azds-route-as: azureuser` 為 HTTP 標頭。
1. *Services*中的 microsoft.devspaces proxy 容器會將要求路由傳送至*services* pod 中的*services*應用程式容器。
1. *Services. >azureuser* pod 中的*services*應用程式會呼叫*serviceB*。 *Services*應用程式也包含可保留現有標頭的程式碼 `azds-route-as` ，在此案例中為 `azds-route-as: azureuser` 。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會收到要求，並根據標頭的值來查閱*serviceB*的 IP `azds-route-as` 。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器找不到*serviceB. >azureuser*的 IP。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會查閱父空間中*serviceB*的 IP，也就是*ServiceB。預設值為。*
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會尋找*serviceB*的 IP，並將要求路由傳送至*serviceB. 預設*pod。
1. *ServiceB*中的 microsoft.devspaces-proxy 容器會收到要求，並將要求路由傳送至 serviceB 中的*serviceB*應用程式容器。*預設*pod。
1. *ServiceB*中的*serviceB*應用程式會將回應傳回至*services. >azureuser* pod。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會接收回應，並將回應路由傳送至*services. >azureuser* pod 中的*services*應用程式容器。
1. *Services*應用程式會接收回應，然後傳回其本身的回應。
1. *Services. >azureuser* pod 中的 microsoft.devspaces-proxy 容器會接收來自*services*應用程式容器的回應，並將回應路由傳送至叢集以外的原始呼叫端。

非 HTTP 的所有其他 TCP 流量都會通過未修改的輸入控制器和 microsoft.devspaces proxy 容器。

## <a name="sharing-a-dev-space"></a>共用開發人員空間

使用小組時，您可以在整個小組之間共用開發空間，並建立衍生的 dev spaces。 擁有開發人員空間資源群組參與者存取權的任何人都可以使用開發空間。

您也可以建立衍生自其他開發人員空間的新開發人員空間。 當您建立衍生的開發空間時， *azds.io/parent-space=PARENT-SPACE-NAME* 標籤會加入至衍生的開發空間的命名空間。 此外，來自父系開發空間的所有應用程式都會與衍生的開發空間共用。 如果您將應用程式的更新版本部署至衍生的開發空間，它只會存在於衍生的開發空間中，而父系開發空間將不會受到影響。 您最多可以有三個層級的衍生 dev spaces 或 *祖* 系空間。

衍生的開發空間也會在其本身的應用程式與從其父系共用的應用程式之間，以智慧方式路由傳送要求。 路由的運作方式是嘗試將要求路由至衍生開發空間中的應用程式，並從父開發人員空間回到共用應用程式。 如果應用程式不在父空間中，則路由會切換回祖系空間中的共用應用程式。

例如：
* 開發人員空間 *預設值* 具有應用程式 *services* 和 *serviceB*。
* 開發空間 *>azureuser* 是衍生自 *預設*。
* *Services*的更新版本會部署至 *>azureuser*。

使用 *>azureuser*時，對 *services* 的所有要求都會路由至 *>azureuser*中的更新版本。 系統會先嘗試將*serviceB*的要求路由至*serviceB*的 *>azureuser*版本。 因為它不存在，所以會路由傳送至 *預設* 版本的 *serviceB*。 如果移除 *>azureuser* 版本的 *services* ，則所有 *services* 的要求都會改為使用 *預設* 的 *services*版本。

## <a name="next-steps"></a>後續步驟

若要查看 Azure Dev Spaces 如何使用路由來提供快速反復專案和開發的範例，請參閱 [使用 Azure Dev Spaces 遠端偵錯程式碼的運作方式][how-it-works-remote-debugging]。


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md