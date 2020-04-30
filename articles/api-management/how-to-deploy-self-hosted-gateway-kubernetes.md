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
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205101"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>將自我裝載閘道部署至 Kubernetes

本文說明將 Azure API 管理的自我裝載閘道元件部署至 Kubernetes 叢集的步驟。

## <a name="prerequisites"></a>先決條件

- 完成下列快速入門：[建立 AZURE API 管理實例](get-started-create-service-instance.md)
- 建立 Kubernetes 叢集。
> [!TIP]
> [單一節點](https://kubernetes.io/docs/setup/#learning-environment)叢集適用于開發和評估用途。 在內部部署或雲端中使用[Kubernetes 認證](https://kubernetes.io/partners/#conformance)的多節點叢集，以用於生產環境工作負載。
- [在您的 API 管理實例中布建自我裝載的閘道資源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-to-kubernetes"></a>部署至 Kubernetes

1. 從 [**部署和基礎結構**] 底下選取 [**閘道**]。
2. 選取您想要部署的自我裝載閘道資源。
3. 選取 [**部署**]。
4. 請注意，[**權杖**] 文字方塊中的存取權杖會使用預設的**到期**和**秘密金鑰**值自動產生。 如有需要，請在其中一個或兩個控制項中挑選所需的值，以產生新的權杖。
5. 選取 [**部署腳本**] 底下的 [ **Kubernetes** ] 索引標籤。
6. 按一下<的**閘道名稱> yml**檔案連結並下載 YAML 檔案。
7. 選取位於 [**部署**] 文字方塊右下角的 [ `kubectl` **複製**圖示]，將命令儲存到剪貼簿。
8. 將命令貼到 [終端機] （或命令）視窗。 第一個命令會建立 Kubernetes 秘密，其中包含在步驟4中產生的存取權杖。 第二個命令會將步驟6中所下載的設定檔套用至 Kubernetes 叢集，並預期檔案會出現在目前的目錄中。
9. 執行命令，以在[預設命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)中建立必要的 Kubernetes 物件，並從從 Microsoft container Registry 下載的[容器映射](https://aka.ms/apim/sputnik/dhub)啟動自我裝載閘道 pod。
10. 執行命令顯示鈴以檢查部署是否成功。 請注意，您可能需要花一點時間來建立所有物件，並將 pod 初始化。
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. 執行命令顯示鈴，以檢查是否已成功建立服務。 請注意，您的服務 Ip 和埠將會不同。
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. 返回 Azure 入口網站並選取 **[總覽**]。
13. **狀態**顯示綠色核取記號圖示，後面接著符合 YAML 檔案中指定之複本數目的節點計數，確認已部署的自我裝載閘道 pod 已成功與 API 管理服務通訊，並具有一般的「心跳」。

![閘道狀態](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 執行<code>kubectl logs deployment/<gateway-name></code>命令，以從隨機選取的 pod （如果有一個以上）來查看記錄。
> 執行<code>kubectl logs -h</code>一組完整的命令選項，例如如何查看特定 pod 或容器的記錄。

## <a name="production-deployment-considerations"></a>生產部署考慮

### <a name="access-token"></a>存取權杖
如果沒有有效的存取權杖自我裝載閘道，就無法從相關聯的 API 管理服務的設定資料端點存取和下載設定。 存取權杖的有效時間上限為30天。 必須重新產生，並以手動方式或透過自動化來設定叢集，然後才會到期。 自動化權杖重新整理時，請使用此[管理 API 作業](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken)來產生新的權杖。 請遵循此[連結](https://kubernetes.io/docs/concepts/configuration/secret)，以取得管理 Kubernetes 秘密的相關資訊。

### <a name="namespace"></a>命名空間
Kubernetes[命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)可協助在多個小組、專案或應用程式之間分割單一叢集。 命名空間會提供資源和名稱的範圍，而且可以與資源配額和存取控制原則相關聯。
在 Azure 入口網站中提供的命令會在**預設**命名空間中建立自我裝載的閘道資源，這會自動建立、存在於每個叢集中，而且無法刪除。
請考慮建立自我裝載閘道[，並](https://kubernetesbyexample.com/ns/)將其部署到生產環境中的個別命名空間。

### <a name="number-of-replicas"></a>複本數目
適用于生產環境的最小複本數目為2。

根據預設，自我裝載閘道會以**RollingUpdate**部署[策略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)進行部署。 請檢查預設值，並考慮明確設定[**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable)和[**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge)欄位，特別是在使用高複本計數時。

### <a name="container-resources"></a>容器資源
根據預設，Azure 入口網站中提供的 YAML 檔案不會指定容器資源要求。

您無法可靠地預測並建議每個容器的 CPU 和記憶體資源數量，以及因應許多因素而支援特定工作負載所需的複本數目，例如：

- 叢集執行所在的特定硬體
- 虛擬化的目前狀態和類型
- 並行用戶端連接的數目和速率
- 要求率
- 已設定的原則種類和數目
- 裝載大小，以及裝載是否已緩衝處理或資料流程處理
- 後端服務延遲

我們建議您將資源要求設定為2個核心和2個 GiB 做為起點，執行負載測試，並根據結果相應增加/相應放大/縮小。

### <a name="container-image-tag"></a>容器映射標記
Azure 入口網站中提供的 YAML 檔案會使用**最新**的標籤，此標記一律會參考自我裝載閘道容器映射的最新版本。

請考慮在生產環境中使用特定版本標籤，以避免意外升級至較新的版本。

請遵循此[連結](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list)，以查看可用標記的完整清單。

### <a name="dns-policy"></a>DNS 原則
DNS 名稱解析在自我裝載閘道能夠連線到 Azure 中的相依性，以及分派對後端服務的 API 呼叫方面，扮演著重要的角色。

Azure 入口網站中提供的 YAML 檔案會套用預設的[**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)原則，這會導致叢集 DNS 無法解析名稱解析要求，以轉送至繼承自節點的上游 dns 伺服器。

請遵循此[連結](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service)來瞭解 Kubernetes 中的名稱解析，並考慮根據您的設定，自訂[DNS 原則](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)或 D[NS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config)設定。

### <a name="configuration-backup"></a>設定備份
請遵循此[連結](self-hosted-gateway-overview.md#connectivity-to-azure)，以瞭解暫時性的 Azure 連線中斷時的自我裝載閘道行為。
設定自我裝載閘道容器的本機儲存磁片區，讓它可以保存最新下載之設定的備份複本，如果連線已關閉，請在重新開機時使用。 磁片區掛接路徑必須是<code>/apim/config</code>。 請參閱[這裡](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)的範例。
若要瞭解 Kubernetes 中的儲存體，請遵循此[連結](https://kubernetes.io/docs/concepts/storage/volumes/)。

### <a name="local-logs-and-metrics"></a>本機記錄和計量
自我裝載閘道會將遙測資料傳送至[Azure 監視器](api-management-howto-use-azure-monitor.md)，並在相關聯的 API 管理服務中，依據每個設定來[Azure 應用程式深入](api-management-howto-app-insights.md)解析。
當[azure](self-hosted-gateway-overview.md#connectivity-to-azure)的連線暫時遺失時，遙測到 azure 的流程會中斷，而且在中斷期間會遺失資料。
請考慮[設定本機監視](how-to-configure-local-metrics-logs.md)，以確保能夠觀察 API 流量，並防止在 Azure 連線中斷期間遺失遙測。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)