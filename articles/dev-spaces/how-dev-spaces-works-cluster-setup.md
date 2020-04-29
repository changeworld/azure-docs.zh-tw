---
title: 設定 Azure Dev Spaces 叢集的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 說明如何設定適用于 Azure Dev Spaces 的 Azure Kubernetes Service 叢集
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241721"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>設定 Azure Dev Spaces 叢集的運作方式

Azure Dev Spaces 提供多種方式來快速反復查看和 Kubernetes 應用程式，並在 Azure Kubernetes Service （AKS）叢集上與您的小組共同作業。 其中一種方式是在 AKS 叢集上啟用 Azure Dev Spaces，讓您可以[直接在叢集上執行服務][how-it-works-up]，並使用[額外的網路和路由功能][how-it-works-routing]。 本文說明當您準備叢集並啟用 Azure Dev Spaces 時，會發生什麼事。

## <a name="prepare-your-aks-cluster"></a>準備您的 AKS 叢集

若要為 Dev Spaces 準備 AKS 叢集，請確認您的 AKS 叢集位於[Azure Dev Spaces 支援][supported-regions]的區域中，而且您正在執行 Kubernetes 1.10.3 或更新版本。 若要從 Azure 入口網站啟用叢集上的 Azure Dev Spaces，請流覽至您的叢集，然後按一下 [ *Dev Spaces*]，將 [*使用 dev spaces* ] 變更為 *[是]*，然後按一下 [*儲存*]。 您也可以藉由執行，從 Azure CLI 啟用`az aks use-dev-spaces`Azure Dev Spaces。

如需為 Dev Spaces 設定 AKS 叢集的範例，請參閱[小組開發快速入門][quickstart-team]。

當您的 AKS 叢集上啟用了 Azure Dev Spaces 時，它會為您的叢集安裝控制器。 控制器位於您的 AKS 叢集之外。 它會驅動用戶端工具與 AKS 叢集之間的行為和通訊。 一旦啟用後，您就可以使用用戶端工具與控制器進行互動。

控制器會執行下列動作：

* 管理開發人員空間的建立和選取。
* 安裝您應用程式的 Helm 圖，並建立 Kubernetes 物件。
* 建立應用程式的容器映射。
* 將您的應用程式部署至 AKS。
* 當您的原始程式碼變更時，會執行累加式組建並重新啟動。
* 記錄管理檔和 HTTP 追蹤。
* 將 stdout 和 stderr 轉送至用戶端工具。
* 設定應用程式在空間內的路由，以及跨父系和子空間。

控制器是叢集外的個別 Azure 資源，並會對叢集中的資源執行下列動作：

* 建立或指定要做為開發人員空間使用的 Kubernetes 命名空間。
* 移除名為*azds*的任何 Kubernetes 命名空間（如果有的話），並建立一個新的。
* 部署 Kubernetes webhook 設定。
* 部署 webhook 許可伺服器。

它會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces 準備叢集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必須至少有一個開發人員空間。 Azure Dev Spaces 在您的 AKS 叢集中使用 Dev Spaces 的 Kubernetes 命名空間。 安裝控制器時，它會提示您建立新的 Kubernetes 命名空間，或選擇現有的命名空間做為您的第一個開發人員空間。 根據預設，控制器會提供將現有的*預設*Kubernetes 命名空間升級為您的第一個開發人員空間。

當命名空間指定為開發人員空間時，控制器會將*azds.io/space=true*標籤新增至該命名空間，以將其識別為開發人員空間。 在準備好叢集之後，預設會選取您建立或指定的初始開發人員空間。 選取空間時，Azure Dev Spaces 會使用它來建立新的工作負載。

您可以使用用戶端工具來建立新的開發人員空間，並移除現有的開發人員空間。 由於 Kubernetes 的限制，無法移除*預設*的開發人員空間。 控制器也會移除名為*azds*的任何現有 Kubernetes 命名空間，以`azds`避免與用戶端工具所使用的命令發生衝突。

Kubernetes webhook 許可伺服器是用來在部署檢測時，將具有三個容器的 pod 插入： devspaces proxy 容器、devspaces proxy-init 容器和 devspaces 組建容器。 **這三個容器都是在 AKS 叢集上以根存取的方式執行。** 它們也會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces Kubernetes webhook 許可伺服器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces proxy 容器是一個側車容器，可處理進出應用程式容器的所有 TCP 流量，並協助執行路由。 如果使用了某些空格，devspaces proxy 容器會將 HTTP 訊息重排。 例如，它可以協助在父和子空間中的應用程式之間路由傳送 HTTP 訊息。 所有非 HTTP 流量都會透過未修改的 devspaces-proxy 傳遞。 Devspaces proxy 容器也會記錄所有輸入和輸出 HTTP 訊息，並將它們以追蹤的形式傳送給用戶端工具。 然後，開發人員可以看到這些追蹤來檢查應用程式的行為。

Devspaces-proxy-init 容器是一個[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它會根據空間階層，將額外的路由規則新增至應用程式的容器。 它會在應用程式容器的 */etc/resolv.conf*檔啟動之前，先更新並進行 iptables 設定，藉以新增路由規則。 */Etc/resolv.conf*的更新可讓您在父空間中解析服務的 DNS。 Iptables 設定更新可確保進出應用程式容器的所有 TCP 流量都會透過 devspaces-proxy 來路由傳送。 除了 Kubernetes 新增的規則之外，devspaces 中的所有更新-proxy-init 也會發生。

Devspaces 組建容器是 init 容器，並已裝載專案原始程式碼和 Docker 通訊端。 專案原始碼和 Docker 的存取權可讓 pod 直接建立應用程式容器。

> [!NOTE]
> Azure Dev Spaces 使用相同的節點來建立應用程式的容器，並加以執行。 因此，Azure Dev Spaces 不需要外部容器登錄來建立和執行您的應用程式。

Kubernetes webhook 許可伺服器會接聽在 AKS 叢集中建立的任何新 pod。 如果該 pod 部署到具有*azds.io/space=true*標籤的任何命名空間，它會插入該 pod 與其他容器。 只有在使用用戶端工具執行應用程式的容器時，才會插入 devspaces-組建容器。

準備好 AKS 叢集之後，您可以使用用戶端工具來準備和執行您在開發人員空間中的程式碼。

## <a name="client-side-tooling"></a>用戶端工具

用戶端工具可讓使用者：
* 產生應用程式的 Dockerfile、Helm 圖表和 Azure Dev Spaces 設定檔。
* 建立父系和子開發人員空間。
* 告知控制器建立並啟動您的應用程式。

當您的應用程式正在執行時，用戶端工具也會：
* 從您在 AKS 中執行的應用程式接收並顯示 stdout 和 stderr。
* 使用[埠向前轉送](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)，以允許使用 HTTP：\//localhost. 對您的應用程式進行 web 存取
* 在 AKS 中，將偵錯工具附加至您正在執行的應用程式。
* 針對累加組建偵測到變更時，將原始程式碼同步處理至您的開發人員空間，以加速反復專案。
* 可讓您將開發人員機器直接連線到 AKS 叢集。

您可以使用命令列中的用戶端工具做為`azds`命令的一部分。 您也可以搭配使用用戶端工具與：

* 使用[Azure Dev Spaces 延伸](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)模組 Visual Studio Code。
* [適用於 Kubernetes 的 Visual Studio Tools](https://aka.ms/get-vsk8stools)的 Visual Studio。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用用戶端工具在您的開發人員空間中準備和執行程式碼，請參閱[準備專案以進行 Azure Dev Spaces 的運作方式][how-it-works-prep]。

若要開始使用 Azure Dev Spaces 進行小組開發，請參閱[Azure Dev Spaces 快速入門中的小組開發][quickstart-team]。

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md