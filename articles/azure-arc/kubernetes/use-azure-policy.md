---
title: 使用 Azure 原則大規模套用叢集設定 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure 原則大規模套用叢集設定
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779954"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>使用 Azure 原則大規模套用叢集設定 (預覽)

## <a name="overview"></a>概觀

使用 Azure 原則，強制對每個 `Microsoft.Kubernetes/connectedclusters` 或啟用 Git-Ops 之 `Microsoft.ContainerService/managedClusters` 資源套用特定的 `Microsoft.KubernetesConfiguration/sourceControlConfigurations`。  為了使用 Azure 原則，您選取現有的原則定義，並建立原則指派。  建立原則指派時，您會設定指派的範圍：這將是 Azure 資源群組或訂用帳戶。  您也可以針對將建立的 `sourceControlConfiguration` 設定參數。  建立指派之後，原則引擎將會識別位於範圍內的所有 `connectedCluster` 或 `managedCluster` 資源，並將 `sourceControlConfiguration` 套用至每一個。

如果您使用多個 Git 存放庫作為每個叢集的真實來源 (例如，一個適用於中央 IT/叢集操作員的存放庫，以及其他適用於應用程式小組的存放庫)，您可以使用多個原則指派來啟用此項，並將每個原則指派設定為使用不同的 Git 存放庫。

## <a name="create-a-custom-policy-definition"></a>建立自訂原則定義

1. 在 Azure 入口網站中，瀏覽至 [原則]，然後在提要欄位的 [製作] 區段中，選取 [定義]。
2. 選取 [+ 原則定義]。
3. 將 [定義位置] 設定為您的訂用帳戶或管理群組。  這將決定可使用原則定義的最大範圍。
4. 為規則賦予**名稱**和**描述**。
5. 在 [類別] 底下，選擇 [建立新的]，然後寫入「Kubernetes 叢集 - Azure Arc」
6. 在 [原則規則] 編輯方塊中，複製/貼上此[範例原則定義](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json)的內容。
7. [儲存]。

完成工作之後，不需要這個建立自訂原則定義的步驟，就能讓此原則成為內建原則。

## <a name="create-a-policy-assignment"></a>建立原則指派

1. 在 Azure 入口網站中，瀏覽至 [原則]，然後在提要欄位的 [製作] 區段中，選取 [定義]。
2. 尋找您剛建立的定義，並加以選取。
3. 在頁面動作中，選取 [指派]。
4. 將 [範圍] 設定為要套用原則指派的管理群組、訂用帳戶或資源群組。
5. 如果您想要從原則範圍中排除任何資源，則設定**排除**。
6. 為原則指派賦予**名稱**和**描述**，讓您能夠輕鬆識別該原則。
7. 確定已將 [原則強制執行] 設定為 [已啟用]。
8. 選取 [下一步] 。
9. 設定將在 `sourceControlConfiguration` 建立期間使用的參數值。
10. 選取 [下一步] 。
11. 啟用 [建立補救工作]。
12. 確保已核取 [建立受控識別]，而且該身分識別將具有**參與者**權限。  如需您所需權限的詳細資訊，請參閱[此文件](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)和[此文件中的註解](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) \(部分機器翻譯\)。
13. 選取 [檢閱 + 建立]。

建立原則指派之後，針對位於指派範圍內任何新的 `connectedCluster` 資源 (或已安裝 GitOps 代理程式的 `managedCluster` 資源)，將套用 `sourceControlConfiguration`。  針對現有叢集，您將必須手動執行補救工作。  通常需要 10-20 分鐘的時間，原則指派才會生效。

## <a name="verify-a-policy-assignment"></a>驗證原則指派

1. 在 Azure 入口網站中，瀏覽至您的其中一個 `connectedCluster` 資源，然後在提要欄位的 [設定] 區段中，選取 [原則] (尚未實作 AKS 受控叢集的 UX，但即將推出)。
2. 在清單中，您應該會看到您先前所建立的原則指派，而且**合規性狀態**應該「符合規範」。
3. 在提要欄位的 [設定] 區段中，選取 [設定]。
4. 在清單中，您應該會看到原則指派所建立的 `sourceControlConfiguration`。
5. 使用 **kubectl** 來詢問叢集：您應該會看到 `sourceControlConfiguration` 所建立的命名空間和成品。
6. 您應該會在 5 分鐘內，於叢集中看到所設定 Git 存放庫資訊清單中所述的成品。

## <a name="next-steps"></a>後續步驟

* [為已啟用 Arc 之 Kubernetes 叢集的容器設定 Azure 監視器](./deploy-azure-monitor-for-containers.md)
