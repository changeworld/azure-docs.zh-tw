---
title: 如何搭配 Azure Dev Spaces 使用 kubectl
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: 瞭解如何在 Azure Kubernetes Service 叢集的開發人員空間內使用已啟用 Azure Dev Spaces 的 kubectl 命令
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75438372"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>使用 Kubectl 搭配 Azure Dev Spaces

您可以存取 Azure Dev Spaces 內的 Kubernetes 叢集，並且使用像是 `kubectl` 的現有 Kubernetes 工具。

執行 `az aks use-dev-spaces` 命令會自動為您新增 `kubectl` 組態內容，因此 kubectl 應該已連線至您的 Azure Dev Spaces Kubernetes 叢集。 範例：
- 確認目前的內容：`kubectl config current-context`
- 列出所有可用的內容：`kubectl config get-contexts`。 
- 變更內容：`kubectl config use-context <context-name>`
- 檢視 Kubernetes 儀表板：執行 `kubectl proxy`，然後開啟瀏覽器前往此命令發出的位址 (將 `/ui` 附加至 URL 以瀏覽到 Kubernetes 儀表板)。
- 列出預設 Azure Dev Spaces 空間中名為*default*的執行中服務：`kubectl get services --namespace=default`

