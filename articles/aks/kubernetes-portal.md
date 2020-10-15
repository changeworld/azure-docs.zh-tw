---
title: '從 Azure 入口網站 (Preview 存取 Kubernetes 資源) '
description: 瞭解如何與 Kubernetes 資源互動，以管理 Azure 入口網站中 (AKS) 叢集的 Azure Kubernetes Service。
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 09/21/2020
ms.author: lahugh
ms.openlocfilehash: ce51e76829c19def1c1603b1a88592d1e683ccae
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070634"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>從 Azure 入口網站 (Preview 存取 Kubernetes 資源) 

Azure 入口網站包含 Kubernetes 資源檢視器 (預覽) ，以便輕鬆存取 Azure Kubernetes Service (AKS) 叢集中的 Kubernetes 資源。 從 Azure 入口網站觀看 Kubernetes 資源可減少 Azure 入口網站與命令列工具之間的內容切換 `kubectl` ，以簡化查看和編輯 Kubernetes 資源的體驗。 資源檢視器目前包含多個資源類型，例如部署、pod 和複本集。

Azure 入口網站中的 Kubernetes 資源檢視會取代 [AKS 儀表板附加][kubernetes-dashboard]元件，該附加元件已設定為取代。

>[!NOTE]
>[私人 Azure Kubernetes Service](./private-clusters.md)叢集目前不支援 capabilty。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>必要條件

若要查看 Azure 入口網站中的 Kubernetes 資源，您需要 AKS 叢集。 所有叢集都受到支援，但如果使用 Azure Active Directory (Azure AD) 整合，則您的叢集必須使用 [AKS 管理的 Azure AD 整合][aks-managed-aad]。 如果您的叢集使用舊版 Azure AD，您可以在入口網站中或使用 [Azure CLI][cli-aad-upgrade]升級您的叢集。

## <a name="view-kubernetes-resources"></a>查看 Kubernetes 資源

若要查看 Kubernetes 資源，請流覽至您在 Azure 入口網站中的 AKS 叢集。 左邊的導覽窗格可用來存取您的資源。 這些資源包括：

- **命名空間** 會顯示叢集的命名空間。 [命名空間] 清單頂端的篩選器可讓您快速篩選和顯示您的命名空間資源。
- **工作負載** 會顯示部署至您叢集的部署、pod、複本集和 daemon 集合的相關資訊。 下列螢幕擷取畫面顯示範例 AKS 叢集中的預設系統 pod。
- **服務和 ingresses** 會顯示您所有叢集的服務和輸入資源。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>部署應用程式

在此範例中，我們將使用我們的範例 AKS 叢集，從 [AKS 快速入門][portal-quickstart]部署 Azure 投票應用程式。

1. 從 (命名空間、工作負載或服務和 ingresses) 的任何資源查看中選取 [ **新增** ]。
1. 從 [AKS 快速入門][portal-quickstart]中貼上 Azure 投票應用程式的 YAML。
1. 選取 [YAML 編輯器] 底部的 [ **新增** ] 以部署應用程式。 

新增 YAML 檔案之後，資源檢視器會顯示已建立的兩個 Kubernetes 服務：內部服務 (azure-投票) ，而外部服務 (azure-投票前) 以存取 Azure 投票應用程式。 外部服務包含連結的外部 IP 位址，因此您可以在瀏覽器中輕鬆地查看應用程式。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>監視部署見解

已啟用 [容器 Azure 監視器的][enable-monitor] AKS 叢集可以快速地查看部署見解。 在 Kubernetes 資源檢視中，使用者可以看到個別部署的即時狀態，包括 CPU 和記憶體使用量，以及轉換至 Azure 監視器，以取得更深入的資訊。 以下是範例 AKS 叢集中的部署見解範例：

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。" lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>編輯 YAML

Kubernetes 資源檢視也包含 YAML 編輯器。 內建的 YAML 編輯器表示您可以在入口網站中更新或建立服務和部署，並立即套用變更。

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。":::

編輯 YAML 之後，請選取 [ **審核] + [儲存**]、確認變更，然後重新儲存，以套用變更。

>[!WARNING]
> 不建議透過 UI 或 CLI 執行直接生產變更，您應該利用 [持續整合 (CI) 和持續部署 (CD) 最佳做法](kubernetes-action.md)。 Azure 入口網站的 Kubernetes 管理功能和 YAML 編輯器是為了學習和試驗開發和測試設定中的新部署所設計。

## <a name="troubleshooting"></a>疑難排解

本節說明常見的問題和疑難排解步驟。

### <a name="unauthorized-access"></a>未經授權的存取

若要存取 Kubernetes 資源，您必須能夠存取 AKS 叢集、Kubernetes API 和 Kubernetes 物件。 確定您是叢集系統管理員或具有適當許可權的使用者，以存取 AKS 叢集。 如需叢集安全性的詳細資訊，請參閱 [AKS 的存取和身分識別選項][concepts-identity]。

>[!NOTE]
> Azure 入口網站中的 kubernetes 資源檢視僅支援受 [管理 aad](managed-aad.md) 的叢集或啟用非 AAD 的叢集。 如果您使用已啟用受控 AAD 的叢集，您的 AAD 使用者或身分識別必須擁有各自的角色/角色系結來存取 kubernetes API，以及提取[使用者 `kubeconfig` ](control-kubeconfig-access.md)的許可權。

### <a name="enable-resource-view"></a>啟用資源檢視

針對現有的叢集，您可能需要啟用 Kubernetes 資源查看。 若要啟用資源檢視，請依照入口網站中的叢集提示進行。

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Kubernetes 顯示在 Azure 入口網站中的 pod 資訊。" lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> 您可以新增 [**api 伺服器授權 IP 範圍**](api-server-authorized-ip-ranges.md) 的 AKS 功能，以將 api 伺服器存取限制為僅限防火牆的公用端點。 這類叢集的另一個選項是更新 `--api-server-authorized-ip-ranges` ，以包含本機用戶端電腦或 IP 位址範圍 (的存取權，) 流覽入口網站。 為了允許此存取，您需要電腦的公用 IPv4 位址。 您可以使用下列命令找到此位址，或在網際網路瀏覽器中搜尋「我的 IP 位址」。
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>後續步驟

本文說明如何存取 AKS 叢集的 Kubernetes 資源。 請參閱 [部署和 YAML 資訊清單][deployments] ，以深入瞭解叢集資源，以及使用 Kubernetes 資源檢視器存取的 YAML 檔案。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md