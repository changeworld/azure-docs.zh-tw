---
title: 異地複寫登錄
description: 開始建立和管理異地複寫的Azure container registry，讓登錄能夠使用多重主要區域複本服務多個區域。 異地複寫是高階服務層的一項功能。
author: stevelas
ms.topic: article
ms.date: 05/11/2020
ms.author: stevelas
ms.openlocfilehash: 315de5151547c4339255639cb65d1be30f7213ff
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247127"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure 容器登錄中的異地複寫

公司想要本機存在或熱備份時，選擇從多個 Azure 區域執行服務。 最佳做法是將容器登錄中放入每個區域，其中執行映像以允許網路關閉作業、啟用快速、可靠的映像圖層傳輸。 異地複寫可讓 Azure 容器登錄成為單一登錄、服務包含多個區域登錄的多重主要區域。 

異地複寫登錄提供下列優點：

* 可跨多個區域使用單一登錄/映像/標記名稱
* 從區域部署進行網路關閉登錄存取
* 沒有其他輸出費用，因為映像是取自與容器主機相同區域中的本機複寫登錄
* 跨多個區域單一管理登錄

> [!NOTE]
> 如果您需要維護多個 Azure容器映像中的容器映像複本，Azure Container Registry 也支援[映像匯入](container-registry-import-images.md)。 例如，在 DevOps 工作流程中，您可以從開發登錄將映像匯入到生產環境登錄，完全不需要使用 Docker 命令。
>

## <a name="example-use-case"></a>使用案例範例
Contoso 會執行位在美國、加拿大和歐洲的公開金鑰存在網站。 為了在這些市場中提供本機和網路關閉內容，Contoso 在美國西部、美國東部、加拿大中部和歐洲西部執行 [Azure Kubernetes Service](../aks/index.yml) (AKS) 叢集。 網站應用程式 (部署為 Docker 映像) 會在所有區域利用相同的程式碼和映像。 從資料庫 (基本在每個區域中佈建) 擷取該區域的本機內容。 每個區域部署都會有其資源的唯一設定，例如本機資料庫。

開發小組位於 Seattle WA，利用美國西部的資料中心。

![推入至多個登錄](media/container-registry-geo-replication/before-geo-replicate.png)<br />*推入至多個登錄*

在使用異地複寫功能前，Contoso 在美國西部具有美國型登錄，在西歐有其他登錄。 為了服務這些不同區域，開發小組將映像發送至兩個不同的登錄。

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![從多個登錄提取](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*從多個登錄提取*

多個登錄的常見難題包含：

* 美國東部、美國西部和加拿大中心叢集皆從美國西部登錄擷取，因為其中每個遠端容器主機皆從美國西部資料中心提取資料，因此會產生輸出費用。
* 開發小組必須將映像推送到美國西部和西歐登錄中。
* 開發小組必須設定及維護參考本機登錄之影像名稱所在的每個區域部署。
* 必須為每個區域設定登錄存取。

## <a name="benefits-of-geo-replication"></a>異地複寫的優點

![從異地複寫登錄中提取](media/container-registry-geo-replication/after-geo-replicate-pull.png)

使用 Azure Container Registry 的異地複寫功能來實現這些優點：

* 在所有區域管理單一登錄：`contoso.azurecr.io`
* 管理單一映像部署設定，因為所有區域都使用相同的映像 URL：`contoso.azurecr.io/public/products/web:1.2`
* 推送至單一登錄，同時 ACR 會管理異地複寫。 您可以設定區域 [Webhook](container-registry-webhook.md) 通知自己有特定複本中的事件。

## <a name="configure-geo-replication"></a>設定異地複寫

設定異地複寫是簡單的，只要按一下地圖上的區域。 您也可以使用工具來管理異地複寫，包括 Azure CLI 中的 [az acr replication](/cli/azure/acr/replication) 命令，或使用 [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)部署已啟用異地複寫的登錄。

異地複寫是[進階登錄](container-registry-skus.md)的一項功能。 如果您的登錄還不是進階，您可以在 [Azure 入口網站](https://portal.azure.com)中從基本和標準變更為進階：

![在 Azure 入口網站中切換服務層級](media/container-registry-skus/update-registry-sku.png)

若要設定進階登錄的異地複寫，請登入 Azure 入口網站 ( https://portal.azure.com )。

導覽到 Azure 容器登錄，並選取 [複寫]：

![在 Azure 入口網站的容器登錄 UI 中進行複寫](media/container-registry-geo-replication/registry-services.png)

地圖會顯示，包含所有目前的 Azure 區域：

 ![Azure 入口網站的區域圖](media/container-registry-geo-replication/registry-geo-map.png)

* 藍色六邊形代表目前的複本
* 綠色六邊形代表可能的複本區域
* 灰色六邊形代表尚未提供複寫的 Azure 區域

若要設定複本，選取綠色六邊形，然後選取 [建立]：

 ![在 Azure 入口網站中建立複寫 UI](media/container-registry-geo-replication/create-replication.png)

若要設定其他複本，請選取其他地區的綠色六邊形，然後按一下 [建立]。

ACR 會開始同步設定的複本之間的映像。 完成時，入口網站會反映 [準備]。 入口網站中的複本狀態不會自動更新。 使用 [重新整理] 按鈕以查看更新的狀態。

## <a name="considerations-for-using-a-geo-replicated-registry"></a>使用異地複寫登錄的考量

* 異地複寫登錄中的每個區域在設定完成後，都是獨立的。 Azure Container Registry SLA 會套用至每個異地複寫的區域。
* 當您對異地複寫的登錄推送或提取映像時，背景中的 Azure 流量管理員會考慮網路延遲而將要求傳送至離您最近的區域中的登錄。
* 當您將映像或標記更新推送至最接近的區域之後，Azure Container registry 需要一些時間將資訊清單和層複寫至您選擇加入的其餘區域。 映像愈大，複寫就愈耗時。 各個複寫區域會透過最終的一致性模型同步處理映像和標記。
* 若要管理必須將更新推送至異地複寫登錄的工作流程，建議您設定 [Webhook](container-registry-webhook.md) 來回應推送事件。 您可以在異地複寫的登錄中設定區域 Webhook，來追蹤異地複寫區域之間的推送事件何時完成。
* 為了提供代表內容層的 blob，Azure Container Registry 使用資料端點。 您可以在每個登錄的異地複寫區域中，為您的登錄啟用[專用的資料端點](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)。 這些端點可讓您設定嚴格限定範圍的防火牆存取規則。
* 如果您使用虛擬網路中的[私人連結](container-registry-private-link.md)設定登錄的私人連結，則預設會啟用每個異地複寫區域中的專用資料端點。 

## <a name="delete-a-replica"></a>刪除複本伺服器

設定登錄的複本之後，您可以在不再需要時將它刪除。 使用 Azure 入口網站或其他工具 (例如 Azure CLI 中的 [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) 命令) 刪除複本。

若要在 Azure 入口網站中刪除複本：

1. 導覽到 Azure Container Registry，並選取 [複寫]。
1. 選取複本的名稱，然後選取 [刪除]。 確認您要刪除該複本。

若要使用 Azure CLI 刪除「美國東部」區域中 *myregistry* 的複本：

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>異地複寫價格

異地複寫是 Azure Container Registry 的[進階服務層級](container-registry-skus.md)功能。 當您要複寫登錄到您想要的區域時，您會產生每個區域的進階登錄費用。

在上述範例中，Contoso 會將兩個登錄向下合併成一個，並將複本新增至美國東部、加拿大中部和西歐。 Contoso 應支付每月的次進階費用，不含任何額外的設定或管理。 每個區域現在會在本機提取其映像，改善效能和可靠性，而不會衍生從美國西部到加拿大和美國東部的網路輸出費用。

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>針對使用異地複寫登錄的推送作業進行疑難排解
 
將映像推送至異地複寫登錄的 Docker 用戶端，可能不會將所有映像層及其資訊清單推送至單一複寫區域。 這可能是因為 Azure 流量管理員會將登錄要求路由傳送至最接近網路的複寫登錄。 如果登錄有兩個「鄰近」複寫區域，則映像層和資訊清單可能會散發至兩個網站，因而在驗證資訊清單時造成推送作業失敗。 之所以會發生此問題，原因是某些 Linux 主機上用來解析登錄 DNS 名稱的方式有誤。 此問題不會發生在 Windows 上，因為 Windows 會提供用戶端 DNS 快取。
 
如果發生此問題，有一個解決方案是在 Linux 主機上套用用戶端 DNS 快取，例如 `dnsmasq`。 這有助於確保登錄名稱會以一致的方式進行解析。 如果您是使用 Azure 中的 Linux VM 來推送至登錄，請參閱 [Azure 中 Linux 虛擬機器的 DNS 名稱解析選項](../virtual-machines/linux/azure-dns.md)中的選項。

若要將推送映像時的最接近複本 DNS 解析最佳化，請在與推送作業來源相同的 Azure 區域中設定異地複寫登錄，如果是在 Azure 外部工作，則請在最接近的區域設定。

## <a name="next-steps"></a>後續步驟

簽出三段式教學課程系列，[Azure Container Registry 中的異地複寫](container-registry-tutorial-prepare-registry.md)。 逐步解說建立異地備援登錄、建立容器，然後再使用單一 `docker push` 命令將其部署到多個區域之 適用於容器的 Web Apps 執行個體。

> [!div class="nextstepaction"]
> [Azure 容器登錄中的異地複寫](container-registry-tutorial-prepare-registry.md)
