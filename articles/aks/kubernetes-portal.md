---
title: '從 Azure 入口網站 (Preview 存取 Kubernetes 資源) '
description: 瞭解如何與 Kubernetes 資源互動，以從 Azure 入口網站管理 (AKS) 叢集的 Azure Kubernetes Service。
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 109192efa19605af003dcfb30592c865ce7495b5
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136785"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>從 Azure 入口網站 (Preview 存取 Kubernetes 資源) 

Azure 入口網站包含 Kubernetes 資源檢視器 (預覽) ，可讓您輕鬆存取 Azure Kubernetes Service (AKS) 叢集中的 Kubernetes 資源。 從 Azure 入口網站查看 Kubernetes 資源可減少 Azure 入口網站與命令列工具之間的內容切換 `kubectl` ，進而簡化 Kubernetes 資源的查看和編輯體驗。 資源檢視器目前包含多個資源類型，例如部署、pod 和複本集。

Azure 入口網站中的 Kubernetes 資源檢視會取代[AKS 的儀表板附加][kubernetes-dashboard]元件，這是針對淘汰而設定的。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>先決條件

若要在 Azure 入口網站中查看 Kubernetes 資源，您需要 AKS 叢集。 支援任何叢集，但如果使用 Azure Active Directory (Azure AD) 整合，您的叢集必須使用[AKS 管理的 Azure AD 整合][aks-managed-aad]。 如果您的叢集使用舊版 Azure AD，您可以在入口網站中或透過[Azure CLI][cli-aad-upgrade]升級您的叢集。

## <a name="view-kubernetes-resources"></a>查看 Kubernetes 資源

若要查看 Kubernetes 資源，請流覽至您在 Azure 入口網站中的 AKS 叢集。 左側的流覽窗格用來存取您的資源。 這些資源包括：

- **命名空間**顯示叢集的命名空間。 [命名空間] 清單頂端的篩選提供快速的方式來篩選和顯示您的命名空間資源。
- **工作負載**會顯示部署至叢集的部署、pod、複本集和背景程式集的相關資訊。 下列螢幕擷取畫面顯示範例 AKS 叢集中的預設系統 pod。
- **服務和會輸入**會顯示您叢集的所有服務和輸入資源。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>部署應用程式

在此範例中，我們將使用我們的範例 AKS 叢集，從[AKS 快速入門][portal-quickstart]部署 Azure 投票應用程式。

1. 從任何資源檢視中選取 [**新增**]， (命名空間、工作負載或服務，以及會輸入) 。
1. 從[AKS 快速入門][portal-quickstart]中，貼上 Azure 投票應用程式的 YAML。
1. 選取 [YAML 編輯器] 底部的 [**新增**] 以部署應用程式。 

新增 YAML 檔案之後，資源檢視器會顯示兩個已建立的 Kubernetes 服務：內部服務 (azure-回復) ，而外部服務 (azure 投票) 來存取 Azure 投票應用程式。 外部服務包含連結的外部 IP 位址，因此您可以在瀏覽器中輕鬆地查看應用程式。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure 會將 Azure 入口網站中顯示的應用程式資訊投票。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>監視部署深入解析

已啟用[容器 Azure 監視器的][enable-monitor]AKS 叢集，可以快速地查看部署深入解析。 在 Kubernetes 資源檢視中，使用者可以查看個別部署的即時狀態，包括 CPU 和記憶體使用量，以及轉換至 Azure 監視器以取得更深入的資訊。 以下是來自範例 AKS 叢集的部署深入解析範例：

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure 入口網站中顯示的部署深入解析。" lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>編輯 YAML

Kubernetes 資源檢視也包含 YAML 編輯器。 內建的 YAML 編輯器表示您可以從入口網站中更新或建立服務和部署，並立即套用變更。

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure 入口網站中顯示 Kubernetes 服務的 [YAML 編輯器]。":::

編輯 YAML 之後，請選取 [**審核] + [儲存**]，確認變更，然後再次儲存，以套用變更。

>[!WARNING]
> 不建議透過 UI 或 CLI 執行直接生產變更，您應該利用[持續整合 (CI) 和持續部署 (CD) 最佳做法](kubernetes-action.md)。 Azure 入口網站 Kubernetes 管理功能和 YAML 編輯器是針對在開發和測試設定中學習和試驗新部署所建立。

## <a name="troubleshooting"></a>疑難排解

本節說明常見的問題和疑難排解步驟。

### <a name="unauthorized-access"></a>未經授權的存取

若要存取 Kubernetes 資源，您必須能夠存取 AKS 叢集、Kubernetes API 和 Kubernetes 物件。 請確定您是叢集系統管理員或具有適當許可權的使用者，才能存取 AKS 叢集。 如需叢集安全性的詳細資訊，請參閱[AKS 的存取和身分識別選項][concepts-identity]。

### <a name="enable-resource-view"></a>啟用資源檢視

針對現有的叢集，您可能需要啟用 Kubernetes 資源查看。 若要啟用資源檢視，請遵循您的叢集入口網站中的提示。

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Azure 入口網站訊息以啟用 Kubernetes 資源查看。" lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>後續步驟

本文說明如何存取 AKS 叢集的 Kubernetes 資源。 如需更深入瞭解叢集資源和使用 Kubernetes 資源檢視器存取的 YAML 檔案，請參閱[部署和 YAML 資訊清單][deployments]。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
