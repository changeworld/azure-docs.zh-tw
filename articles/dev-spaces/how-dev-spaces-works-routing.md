---
title: 路由如何與 Azure Dev Spaces 搭配運作
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述 power Azure Dev Spaces 的處理常式，以及路由的運作方式
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: abdd8a573b7e120c5494b8878c2eb19813fc9507
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213410"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由如何與 Azure Dev Spaces 搭配運作

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 當您的專案在開發人員空間中執行之後，Azure Dev Spaces 會為您的專案提供額外的網路和路由功能。

本文說明路由如何與 Dev Spaces 搭配運作。

## <a name="how-routing-works"></a>路由的運作方式

開發人員空間是以 AKS 為基礎，並使用相同的 [網路概念](../aks/concepts-network.md)。 Azure Dev Spaces 也有一個集中式 *ingressmanager* 服務，並將它自己的輸入控制器部署到 AKS 叢集。 *Ingressmanager*服務會監視具有 dev SPACES 的 AKS 叢集，並使用用於路由傳送至應用程式 pod 的輸入物件，來擴大叢集中的 Azure Dev Spaces 輸入控制器。 每個 pod 中的 devspaces proxy 容器都會根據 `azds-route-as` URL，將 HTTP 流量的 HTTP 標頭新增至開發人員空間。 例如，URL 的要求 *http://azureuser.s.default.serviceA.fedcba09...azds.io* 會取得具有的 HTTP 標頭 `azds-route-as: azureuser` 。 Devspaces proxy 容器若已存在，則不會新增 `azds-route-as` 標頭。

從叢集外部對服務發出 HTTP 要求時，要求會移至輸入控制器。 輸入控制器會根據輸入物件和規則，將要求直接路由傳送至適當的 pod。 Pod 中的 devspaces proxy 容器會接收要求、 `azds-route-as` 根據 URL 新增標頭，然後將要求路由傳送至應用程式容器。

當從叢集內的另一個服務對服務發出 HTTP 要求時，要求會先經過呼叫服務的 devspaces proxy 容器。 Devspaces proxy 容器會查看 HTTP 要求，並檢查 `azds-route-as` 標頭。 根據標頭，devspaces proxy 容器會查閱與標頭值相關聯之服務的 IP 位址。 如果找到 IP 位址，devspaces proxy 容器會將要求重設為該 IP 位址。 如果找不到 IP 位址，devspaces proxy 容器會將要求路由傳送至父應用程式容器。

例如，應用程式 *serviceA* 和 *serviceB* 會部署到名為 *default*的父開發人員空間。 *serviceA* 依賴 *serviceB* 並對其進行 HTTP 呼叫。 Azure 使用者會根據名為*azureuser*的*預設*空間來建立子開發人員空間。 Azure 使用者也會將自己的 *serviceA* 版本部署到其子空間。 當提出要求時 *http://azureuser.s.default.serviceA.fedcba09...azds.io* ：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 輸入控制器會查閱與 URL 相關聯之 pod 的 IP，也就是 *serviceA. azureuser*。
1. 輸入控制器會在 Azure 使用者的開發人員空間中尋找 pod 的 IP，並將要求路由傳送至 *serviceA. azureuser* pod。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收要求，並將新增 `azds-route-as: azureuser` 為 HTTP 標頭。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會將要求路由至*serviceA* pod 中的*serviceA*應用程式容器。
1. *ServiceA. azureuser* pod 中的*serviceA*應用程式會呼叫*serviceB*。 *ServiceA*應用程式也包含用來保留現有標頭的程式碼 `azds-route-as` ，在此案例中為 `azds-route-as: azureuser` 。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收要求，並根據標頭的值查閱*serviceB*的 IP `azds-route-as` 。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器找不到*serviceB. azureuser*的 IP。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會在父空間中查詢*serviceB*的 IP，這是*serviceB。預設值*。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會尋找*serviceB*的 IP，並將要求路由傳送至*serviceB. default* pod。
1. *ServiceB*中的 devspaces proxy 容器會接收要求，並將要求路由傳送至*ServiceB*中的*serviceB*應用程式容器。
1. *ServiceB*中的*serviceB*應用程式會傳回*serviceA azureuser* pod 的回應。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收回應，並將回應路由至*serviceA* pod 中的*serviceA*應用程式容器。
1. *ServiceA*應用程式會接收回應，然後傳回自己的回應。
1. *ServiceA. azureuser* pod 中的 devspaces proxy 容器會接收來自*serviceA*應用程式容器的回應，並將回應路由至叢集外的原始呼叫端。

所有其他非 HTTP 的 TCP 流量都會通過未修改的輸入控制器和 devspaces proxy 容器。

## <a name="sharing-a-dev-space"></a>共用開發人員空間

與小組合作時，您可以在整個小組之間共用開發人員空間，並建立衍生的 dev spaces。 具有 dev 空間之資源群組之參與者存取權的任何人都可以使用開發人員空間。

您也可以建立一個衍生自另一個開發人員空間的新開發人員空間。 當您建立衍生的開發人員空間時， *azds.io/parent-space=PARENT-SPACE-NAME* 標籤會加入至衍生的開發人員空間的命名空間。 此外，父開發人員空間中的所有應用程式都會與衍生的開發人員空間共用。 如果您將應用程式的更新版本部署至衍生的開發人員空間，它只會存在於衍生的開發人員空間中，而父開發人員空間則不會受到影響。 您最多可以有三個層級的衍生 dev spaces 或 *祖* 空白字元。

衍生的開發人員空間也會在其本身的應用程式與從其父系共用的應用程式之間，以智慧方式路由傳送要求。 路由的運作方式是嘗試將要求路由至衍生的開發人員空間中的應用程式，並從父開發人員空間回到共用應用程式。 如果應用程式不在父空間中，路由將會切換回祖系空間中的共用應用程式。

例如：
* 開發人員空間的 *預設值* 有應用程式 *serviceA* 和 *serviceB*。
* [開發人員空間] *azureuser* 衍生自 *預設值*。
* *ServiceA*的更新版本會部署到*azureuser*。

使用 *azureuser*時，對 *serviceA* 的所有要求都會路由至 *azureuser*中的更新版本。 *ServiceB*的要求會先嘗試路由至*serviceB*的*azureuser*版本。 因為它不存在，所以會路由傳送至 *預設* 版本的 *serviceB*。 如果移除*serviceA*的*azureuser*版本，所有對*serviceA*的要求都會切換回使用*預設*的*serviceA*版本。

## <a name="next-steps"></a>後續步驟

若要查看 Azure Dev Spaces 如何使用路由來提供快速反復專案和開發的一些範例，請參閱 [使用 Kubernetes 的本機程式如何運作][how-it-works-local-process-kubernetes]、 [如何使用 Azure Dev Spaces 運作來遠端偵錯程式碼][how-it-works-remote-debugging]，以及 [& Azure Kubernetes Service 的 GitHub 動作][pr-flow]。


[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[troubleshooting]: troubleshooting.md