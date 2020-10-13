---
title: 將自我裝載閘道部署至 Kubernetes |Microsoft Docs
description: 瞭解如何將 Azure API 管理的自我裝載閘道元件部署至 Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500125"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>將自我裝載閘道部署至 Kubernetes

本文說明將 Azure API 管理的自我裝載閘道元件部署至 Kubernetes 叢集的步驟。

## <a name="prerequisites"></a>必要條件

- 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
- 建立 Kubernetes 叢集。
   > [!TIP]
   > [單一節點](https://kubernetes.io/docs/setup/#learning-environment) 叢集適用于開發和評估用途。 在內部部署或雲端中，針對生產工作負載使用 [Kubernetes 認證](https://kubernetes.io/partners/#conformance) 的多節點叢集。
- [在您的 API 管理實例中布建自我裝載的閘道資源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-to-kubernetes"></a>部署至 Kubernetes

1. 選取 [**部署] 和 [基礎結構**] 下的**閘道**。
2. 選取您要部署的自我裝載閘道資源。
3. 選取 [ **部署**]。
4. 系統會根據預設的**到期**和**秘密金鑰**值，為您自動產生**權杖**文字方塊中的存取權杖。 如有需要，請選擇其中一個或兩個控制項中的值，以產生新的權杖。
5. 選取 [**部署腳本**] 底下的 [ **Kubernetes** ] 索引標籤。
6. 選取** \<gateway-name\> yml**檔案連結，並下載 YAML 檔案。
7. 選取 [**部署**] 文字方塊右下角的**複製**圖示，將 `kubectl` 命令儲存至剪貼簿。
8. 將命令貼到終端機 (或命令) 視窗。 第一個命令會建立 Kubernetes 秘密，其中包含步驟4中所產生的存取權杖。 第二個命令會將步驟6中下載的設定檔套用至 Kubernetes 叢集，並預期檔案必須位於目前的目錄中。
9. 執行命令以在 [預設命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 中建立必要的 Kubernetes 物件，然後從 Microsoft container Registry 下載的 [容器映射](https://aka.ms/apim/sputnik/dhub) 啟動自我裝載的閘道 pod。
10. 執行下列命令以檢查部署是否成功。 請注意，所有物件都需要花一些時間才能初始化。
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. 執行下列命令，以檢查是否已成功建立服務。 請注意，您的服務 Ip 和埠將會不同。
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. 返回至 Azure 入口網站，然後選取 **[總覽**]。
13. 確認 **狀態** 顯示綠色核取記號，後面接著符合 YAML 檔中所指定複本數目的節點計數。 此狀態表示已部署的自我裝載閘道 pod 已成功與 API 管理服務通訊，且具有一般「信號」。

    ![閘道狀態](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 執行 <code>kubectl logs deployment/<gateway-name></code> 命令以從隨機選取的 pod 查看記錄（如果有一個以上）。
> 執行 <code>kubectl logs -h</code> 一組完整的命令選項，例如如何查看特定 pod 或容器的記錄。

## <a name="production-deployment-considerations"></a>生產部署考慮

### <a name="access-token"></a>存取權杖
如果沒有有效的存取權杖，自我裝載的閘道就無法從關聯的 API 管理服務的端點存取及下載設定資料。 存取權杖的有效時間最多可達30天。 您必須重新產生它，並使用全新的權杖（手動或透過自動化）設定叢集，然後才會過期。

當您要將權杖重新整理自動化時，請使用 [此管理 API](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) 作業來產生新的權杖。 如需管理 Kubernetes 秘密的相關資訊，請參閱 [Kubernetes 網站](https://kubernetes.io/docs/concepts/configuration/secret)。

### <a name="namespace"></a>命名空間
Kubernetes [命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 有助於在多個小組、專案或應用程式之間分割單一叢集。 命名空間提供資源和名稱的範圍。 它們可以與資源配額和存取控制原則相關聯。

Azure 入口網站會提供命令，以在 **預設** 命名空間中建立自我裝載的閘道資源。 此命名空間會自動建立，存在於每個叢集中，而且無法刪除。
請考慮在生產環境中建立自我裝載的閘道 [，並](https://kubernetesbyexample.com/ns/) 將其部署至不同的命名空間。

### <a name="number-of-replicas"></a>複本數目
適用于生產環境的複本數目下限為2。

根據預設，會使用 **RollingUpdate** 部署 [策略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)來部署自我裝載的閘道。 請檢查預設值，並考慮明確設定 [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) 和 [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) 欄位，尤其是當您使用高複本計數時。

### <a name="container-resources"></a>容器資源
根據預設，Azure 入口網站中提供的 YAML 檔案不會指定容器資源要求。

您無法可靠地預測和建議每個容器的 CPU 和記憶體資源數量，以及支援特定工作負載所需的複本數目。 許多因素都是在玩，例如：

- 叢集正在執行的特定硬體。
- 虛擬化的存在和類型。
- 並行用戶端連接的數目和速率。
- 要求速率。
- 已設定的原則種類和數目。
- 承載大小，以及是否要緩衝處理或串流處理承載。
- 後端服務延遲。

建議您將資源要求設定為兩個核心，並將2個 GiB 設定為一個起點。 執行負載測試，並根據結果擴大/縮小或減少/縮小。

### <a name="container-image-tag"></a>容器映射標記
Azure 入口網站中提供的 YAML 檔案會使用 **最新** 的標記。 此標記一律會參考最新版本的自我裝載閘道容器映射。

請考慮在生產環境中使用特定的版本標籤，以避免不慎升級至較新的版本。

您可以 [下載可用標記的完整清單](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)。

### <a name="dns-policy"></a>DNS 原則
在自我裝載的閘道能夠連線到 Azure 中的相依性，並將 API 呼叫分派至後端服務時，DNS 名稱解析扮演了重要角色。

Azure 入口網站中提供的 YAML 檔案會套用預設的 [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 原則。 此原則會導致叢集 DNS 未解析的名稱解析要求轉送到繼承自節點的上游 DNS 伺服器。

若要瞭解 Kubernetes 中的名稱解析，請參閱 [Kubernetes 網站](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)。 請考慮針對您的安裝程式，視需要自訂 [dns 原則](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 或 [dns](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) 設定。

### <a name="external-traffic-policy"></a>外部流量原則
Azure 入口網站中提供的 YAML 檔會將 `externalTrafficPolicy` [服務](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) 物件上的欄位設定為 `Local` 。 這會保留呼叫端 IP 位址 (可在 [要求內容](api-management-policy-expressions.md#ContextVariables) 中存取) 並停用跨節點負載平衡，以消除其所造成的網路躍點。 請注意，此設定可能會導致部署中的流量非對稱散發，且每個節點的閘道 pod 數目不相等。

### <a name="custom-domain-names-and-ssl-certificates"></a>自訂功能變數名稱和 SSL 憑證

如果您使用 API 管理端點的自訂功能變數名稱，特別是當您針對管理端點使用自訂功能變數名稱時，您可能需要更新 yaml 檔案中的值， `config.service.endpoint` 以** \<gateway-name\> **使用自訂功能變數名稱來取代預設功能變數名稱。 請確定可以從 Kubernetes 叢集中自我裝載閘道的 pod 存取管理端點。

在此案例中，如果管理端點所使用的 SSL 憑證不是由已知的 CA 憑證所簽署，您必須確定 CA 憑證是由自我裝載閘道的 pod 所信任。

### <a name="configuration-backup"></a>設定備份
若要瞭解在發生暫時性的 Azure 連線中斷時，自我裝載的閘道行為，請參閱 [自我裝載閘道的總覽](self-hosted-gateway-overview.md#connectivity-to-azure)。

為自我裝載的閘道容器設定本機儲存體磁片區，讓它可以保存最新下載設定的備份複本。 如果連線中斷，儲存磁片區可以在重新開機時使用備份複本。 磁片區掛接路徑必須是 <code>/apim/config</code> 。 請參閱 [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)上的範例。
若要瞭解 Kubernetes 中的儲存體，請參閱 [Kubernetes 網站](https://kubernetes.io/docs/concepts/storage/volumes/)。

### <a name="local-logs-and-metrics"></a>本機記錄和計量
自我裝載閘道會根據相關聯的 API 管理服務中的設定設定，將遙測傳送至 [Azure 監視器](api-management-howto-use-azure-monitor.md) 並 [Azure 應用程式見解](api-management-howto-app-insights.md) 。
當 [azure](self-hosted-gateway-overview.md#connectivity-to-azure) 的連線暫時遺失時，會中斷流向 azure 的遙測流量，且資料會在中斷期間遺失。
請考慮 [設定本機監視](how-to-configure-local-metrics-logs.md) ，以確保能夠觀察 API 流量，並防止在 Azure 連線中斷期間遺失遙測資料。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載的閘道，請參閱 [自我裝載閘道總覽](self-hosted-gateway-overview.md)。
