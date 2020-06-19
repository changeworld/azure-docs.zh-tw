---
title: 如何設定 Azure Dev Spaces 叢集的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 說明如何設定適用於 Azure Dev Spaces 的 Azure Kubernetes Service 叢集
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 6b158ca7f425e8b7c492c27521dba588a508b534
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873549"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>如何設定 Azure Dev Spaces 叢集的運作方式

Azure Dev Spaces 提供了多種方式，可供迅速逐一查看 Kubernetes 應用程式並偵錯，以及在 Azure Kubernetes Service (AKS) 叢集上與您的小組共同作業。 其中一種方式是在您的 AKS 叢集上啟用 Azure Dev Spaces，如此可以[直接在叢集上執行服務][how-it-works-up]並使用[其他網路和路由功能][how-it-works-routing]。 本文說明當您準備叢集並啟用 Azure Dev Spaces 時會發生的情況。

## <a name="prepare-your-aks-cluster"></a>準備您的 AKS 叢集

若要準備適用於 Dev Spaces 的 AKS 叢集，請確認您的 AKS 叢集位於 [Azure Dev Spaces 支援的區域][supported-regions]，且目前執行的是 Kubernetes 1.10.3 或更新版本。 若要從 Azure 入口網站啟用叢集上的 Azure Dev Spaces，請瀏覽至您的叢集，然後按一下 [Dev Spaces]，將 [使用 Dev Spaces] 變更為 [是]，並按一下 [儲存]。 您也可以藉由執行 `az aks use-dev-spaces`，從 Azure CLI 啟用 Azure Dev Spaces。

如需為 Dev Spaces 設定 AKS 叢集的範例，請參閱[小組開發快速入門][quickstart-team]。

在 AKS 叢集上啟用 Azure Dev Spaces 時，會為您的叢集安裝控制器。 控制器位於您的 AKS 叢集範圍之外。 控制器會驅動用戶端工具與 AKS 叢集之間的行為和通訊。 一旦啟用後，就可以透過用戶端工具與控制器進行互動。

控制器會執行下列動作：

* 管理開發空間的建立和選取。
* 安裝應用程式的 Helm 圖表，並建立 Kubernetes 物件。
* 建立應用程式的容器映像。
* 將您的應用程式部署至 AKS。
* 當您的原始程式碼變更時，會執行累加組建並重新啟動。
* 記錄管理檔和 HTTP 追蹤。
* 將 stdout 和 stderr 轉送至用戶端工具。
* 設定應用程式在空間內以及跨父代和子系空間的路由。

控制器是叢集外的個別 Azure 資源，並會對叢集中的資源執行下列動作：

* 建立或指定要做為開發空間使用的 Kubernetes 命名空間。
* 移除名為 azds的任何 Kubernetes 命名空間 (如果有)，並建立新的命名空間。
* 部署 Kubernetes Webhook 設定。
* 部署 Webhook 許可伺服器。

這會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces 準備叢集](media/how-dev-spaces-works/prepare-cluster.svg)

若要使用 Azure Dev Spaces，必須至少有一個開發空間。 Azure Dev Spaces 在您的 AKS 叢集中使用開發空間的 Kubernetes 命名空間。 安裝控制器時，系統會提示您建立新的 Kubernetes 命名空間，或選擇現有的命名空間做為您的第一個開發空間。 根據預設，控制器供應項目會將現有預設 Kubernetes 命名空間升級為您的第一個開發空間。

當命名空間指定為開發空間時，控制器會將 azds.io/space=true 標籤新增至該命名空間，以將其識別為開發空間。 在準備好叢集之後，預設會選取您建立或指定的初始開發空間。 選取空間時，Azure Dev Spaces 會使用這來建立新的工作負載。

您可以使用用戶端工具來建立新的開發空間，並移除現有的開發空間。 由於 Kubernetes 的限制，無法移除預設開發空間。 控制器也會移除名為 azds 的任何現有 Kubernetes 命名空間，以避免與用戶端工具所使用的 `azds` 命令發生衝突。

Kubernetes Webhook 許可伺服器可用來在部署檢測時插入具有三個容器的 Pod：devspaces-proxy 容器、devspaces-proxy-init 容器和 devspaces-build 容器。 **這三個容器都是在 AKS 叢集上以根存取的方式執行。** 這些也會會使用您的 AKS 叢集用來對其他 Azure Dev Spaces 元件進行服務呼叫的相同服務主體。

![Azure Dev Spaces Kubernetes Webhook 許可伺服器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-proxy 容器是側車容器，可處理進出應用程式容器的所有 TCP 流量，並協助執行路由。 如果使用某些空格，devspaces-proxy 容器會將 HTTP 訊息重新路由。 例如，這可以協助在父代和子系空間中的應用程式之間路由傳送 HTTP 訊息。 所有非 HTTP 流量都會透過未修改的 devspaces-proxy 傳遞。 devspaces-proxy 容器也會記錄所有輸入和輸出 HTTP 訊息，並將這些訊息以追蹤的形式傳送給用戶端工具。 然後，開發人員可以看到這些追蹤來檢查應用程式的行為。

devspaces-proxy-init 容器是 [init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，會根據空間階層，將額外的路由規則新增至應用程式的容器。 這會在啟動之前，先更新應用程式容器的 /etc/resolv.conf 檔案和 iptables 設定，以新增路由規則。 /etc/resolv.conf 的更新可讓您對於父代空間中的服務進行的 DNS 解析。 iptables 設定更新可確保進出應用程式容器的所有 TCP 流量都會透過 devspaces-proxy 來路由傳送。 除了 Kubernetes 新增的規則之外，也會發生 devspaces-proxy-init 中的所有更新。

devspaces-build 容器是 init 容器，並已裝載專案原始程式碼和 Docker 通訊端。 專案原始碼和 Docker 的存取權可讓 Pod 直接建立應用程式容器。

> [!NOTE]
> Azure Dev Spaces 使用相同的節點來建立應用程式的容器，並加以執行。 因此，Azure Dev Spaces 不需要外部容器登錄來建立和執行您的應用程式。

Kubernetes Webhook 許可伺服器會接聽在 AKS 叢集中建立的任何新 Pod。 如果該 Pod 部署到具有 azds.io/space=true 標籤的任何命名空間，這會插入該 Pod 與其他容器。 只有在使用用戶端工具執行應用程式的容器時，才會插入 devspaces-build 容器。

準備好 AKS 叢集之後，您可以使用用戶端工具來準備和執行您在開發空間中的程式碼。

## <a name="client-side-tooling"></a>用戶端工具

用戶端工具可讓使用者：
* 產生應用程式的 Dockerfile、Helm 圖表和 Azure Dev Spaces 設定檔。
* 建立父代和子系開發空間。
* 告知控制器建立並啟動您的應用程式。

當您的應用程式正在執行時，用戶端工具也會：
* 從您在 AKS 中執行的應用程式接收並顯示 stdout 和 stderr。
* 使用 [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)，以允許使用 http:\//localhost 對您的應用程式進行 Web 存取。
* 在 AKS 中，將偵錯工具附加至您正在執行的應用程式。
* 針對累加組建偵測到變更時，將原始程式碼同步至您的開發空間，以加速反覆運算。
* 可讓您將開發人員機器直接連線到 AKS 叢集。

您可以使用命令列中的用戶端工具做為 `azds` 命令的一部份。 您也可以搭配使用用戶端工具與：

* 使用 [Azure Dev Spaces 擴充功能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)的 Visual Studio Code。
* 使用 Azure 開發工作負載的 Visual Studio。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用用戶端工具來準備和執行開發空間中的程式碼，請參閱[準備 Azure Dev Spaces 的專案的運作方式][how-it-works-prep]。

若要開始使用 Azure Dev Spaces 進行小組開發，請參閱 [Azure Dev Spaces 快速入門中的小組開發][quickstart-team]。

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md