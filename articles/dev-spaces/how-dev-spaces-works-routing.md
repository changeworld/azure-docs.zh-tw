---
title: 路由如何與 Azure 開發空間配合使用
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述為 Azure 開發空間供電的過程以及路由的工作原理
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241383"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由如何與 Azure 開發空間配合使用

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式，並與您的團隊協作處理 Azure Kubernetes 服務 （AKS） 群集。 專案在開發空間中運行後，Azure 開發人員空間會為專案提供其他網路和路由功能。

本文介紹了路由如何使用開發空間。

## <a name="how-routing-works"></a>路由的工作原理

開發空間構建在 AKS 之上，使用相同的[網路概念](../aks/concepts-network.md)。 Azure 開發人員空間還具有集中*式入口管理器*服務，並將自己的入口控制器部署到 AKS 群集。 *入口管理器*服務使用開發空間監視 AKS 群集，並將群集中的 Azure 開發空間入口控制器與入口物件增強以路由到應用程式窗格。 每個窗格中的開發人員空間代理容器將 HTTP`azds-route-as`流量的 HTTP 標頭添加到基於 URL 的開發空間。 例如，對 URL*http://azureuser.s.default.serviceA.fedcba09...azds.io*的請求將獲得帶有`azds-route-as: azureuser`的 HTTP 標頭。 如果已存在`azds-route-as`，則 devspace 代理容器不會添加標頭。

當從群集外部向服務發出 HTTP 要求時，該請求將轉到入口控制器。 入口控制器根據其入口物件和規則將請求直接路由到相應的窗格。 窗格中的 devspace 代理容器接收請求，根據 URL 添加`azds-route-as`標頭，然後將請求路由到應用程式容器。

當從群集中的另一個服務向服務發出 HTTP 要求時，請求首先通過調用服務的 devspace 代理容器。 開發空間代理容器查看 HTTP 要求並檢查`azds-route-as`標頭。 根據標頭，開發空間代理容器將查找與標頭值關聯的服務的 IP 位址。 如果找到 IP 位址，則開發空間代理容器將請求重新路由到該 IP 位址。 如果未找到 IP 位址，則 devspace 代理容器將請求路由到父應用程式容器。

例如，應用程式服務*A* *和服務 B*部署到稱為*預設*的父開發空間。 *服務A*依賴于*服務 B*並對此進行 HTTP 調用。 Azure 使用者基於稱為*azureuser*的*預設*空間創建子開發空間。 Azure 使用者還將自己的*服務 A*版本部署到其子空間。 請求時*http://azureuser.s.default.serviceA.fedcba09...azds.io*：

![Azure 開發空間路由](media/how-dev-spaces-works/routing.svg)

1. 入口控制器查找與 URL 關聯的 pod 的 IP，該 URL 是*serviceA.azureuser*。
1. 入口控制器在 Azure 使用者的開發空間中查找 Pod 的 IP，並將請求路由到*服務 A.azureuser* pod。
1. *服務 A.azureuser*窗格中的開發人員空間代理容器接收請求並添加`azds-route-as: azureuser`為 HTTP 標頭。
1. *服務 A.azureuser*窗格中的開發空間代理容器將請求路由到*服務 A.azureuser*窗格中的*服務 A*應用程式容器。
1. *服務 A.azureuser*窗格中的應用程式調用*服務 B*。 *serviceA* *serviceA*應用程式還包含用於保留現有`azds-route-as`標頭的代碼，在這種情況下，該標頭為`azds-route-as: azureuser`。
1. *serviceA.azureuser*窗格中的開發空間代理容器接收請求，並根據標頭的值查找*服務 B*的`azds-route-as`IP。
1. *服務 A.azureuser*窗格中的開發空間代理容器找不到*服務 B.azureuser*的 IP。
1. *serviceA.azureuser*窗格中的開發空間代理容器在父空間中查找服務*B*的 IP，即服務*B.default*。
1. *serviceA.azureuser*窗格中的開發人員空間代理容器查找*服務 B.default*的 IP 並將請求路由到*服務 B.default* pod。
1. *服務 B.default*窗格中的 devspace 代理容器接收請求並將請求路由到*服務 B.default* pod 中的*服務 B*應用程式容器。
1. *服務 B.default* pod 中的*服務 B*應用程式返回對*服務 A.azureuser* pod 的回應。
1. *serviceA.azureuser*窗格中的開發空間代理容器接收回應並將回應路由到*serviceA.azureuser*窗格中的*服務 A*應用程式容器。
1. *服務 應用程式*接收回應，然後返回自己的回應。
1. *serviceA.azureuser*窗格中的 devspace 代理容器接收來自*服務 A*應用程式容器的回應，並將回應路由到群集外部的原始調用方。

所有其他非 HTTP 的 TCP 流量都未經修改地通過入口控制器和開發空間代理容器。

## <a name="sharing-a-dev-space"></a>共用開發空間

與團隊合作時，可以[跨整個團隊共用開發空間](how-to/share-dev-spaces.md)並創建派生開發空間。 具有對開發空間資源組的貢獻者存取權限的任何人都可以使用開發空間。

您還可以創建從另一個開發空間派生的新開發空間。 創建派生開發空間時 *，azds.io/parent-space=PARENT-SPACE-NAME*標籤將添加到派生開發空間的命名空間中。 此外，來自父開發空間的所有應用程式都與派生的開發空間共用。 如果將應用程式的更新版本部署到派生的開發空間，則應用程式將僅存在於派生的開發空間中，並且父開發空間將不受影響。 最多可以有三個級別的派生開發空間或*祖父母*空間。

派生的開發空間還將在自己的應用程式和從其父應用程式共用的應用程式之間智慧路由請求。 路由的工作原理是嘗試將請求路由到派生開發空間中的應用程式，並從父開發空間返回共用應用程式。 如果應用程式不在父空間中，則路由將回退到父級空間中的共用應用程式。

例如：
* 開發空間*預設值*具有應用程式*服務 A* *和服務 B*。
* 開發空間*azureuser*派生自*預設值*。
* *服務 A*的更新版本部署到*azureuser*。

使用*azureuser*時，服務*A*的所有請求都將路由到*azureuser*中的更新版本。 對*服務B*的請求將首先嘗試路由到*服務B*的*azureuser*版本。 由於它不存在，它將路由到*預設*版本的*服務B。* 如果刪除*服務 A*的*azureuser*版本，則所有對*服務 A*的請求都將回退到使用服務*A*的*預設*版本。

## <a name="next-steps"></a>後續步驟

要查看 Azure 開發人員空間如何使用路由來提供快速反覆運算和開發的示例，請參閱[如何將開發電腦連接到開發空間工作][how-it-works-connect]原理、[使用 Azure 開發人員空間遠端偵錯代碼的工作原理][how-it-works-remote-debugging]以及[& Azure Kubernetes 服務的 GitHub 操作][pr-flow]。

要開始使用 Azure 開發人員空間的路由進行團隊開發，請參閱[Azure 開發人員空間中的團隊開發][quickstart-team]快速入門。

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md