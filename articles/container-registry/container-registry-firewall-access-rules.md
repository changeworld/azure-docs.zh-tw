---
title: 防火牆存取規則
description: 設定可以從防火牆後方存取 Azure 容器登錄的規則，方法是允許存取 (「白名單」) REST API 和資料端點網域名稱或服務特定的 IP 位址範圍。
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 679dbcaf30653b855d35825f94e93f87ac68c322
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246974"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>設定可以從防火牆後方存取 Azure 容器登錄的規則

本文說明如何在防火牆上設定規則，以允許存取 Azure 容器登錄。 例如，位於防火牆或 Proxy 伺服器後方的 Azure IoT Edge 裝置可能需要存取容器登錄來提取容器映像。 或者，在內部部署網路中鎖定的伺服器可能需要存取權才能推送映像。

如果您想要只在 Azure 虛擬網路內設定容器登錄的輸入網路存取，請參閱 [設定 Azure 容器登錄的 Azure Private Link](container-registry-private-link.md)。

## <a name="about-registry-endpoints"></a>關於登錄端點

若要將映像或其他成品提取或推送至 Azure 容器登錄，用戶端 (例如 Docker 精靈) 必須透過 HTTPS 與兩個不同的端點進行互動。 若用戶端需從防火牆後方存取登錄，您必須為兩個端點設定存取規則。 這兩個端點都透過連接埠 443 聯繫。

* **登錄 REST API 端點** - 驗證和登錄管理作業會透過登錄的公用 REST API 端點來處理。 此端點是登錄的登入伺服器名稱。 範例： `myregistry.azurecr.io`

* **儲存體 (資料) 端點** - Azure 會代表每個登錄，在 Azure 儲存體帳戶中[配置 blob 儲存體](container-registry-storage.md)，以管理容器映像和其他成品的資料。 當用戶端存取 Azure 容器登錄中的映像層時，會使用登錄所提供的儲存體帳戶端點來提出要求。

如果您的登錄是[異地複寫](container-registry-geo-replication.md)，用戶端可能需要與特定區域或多個複寫區域中的資料端點互動。

## <a name="allow-access-to-rest-and-data-endpoints"></a>允許存取 REST 和資料端點

* **REST 端點** - 允許存取完整登錄登入伺服器名稱、`<registry-name>.azurecr.io` 或相關聯的 IP 位址範圍。
* **儲存體 (資料) 端點** - 允許使用萬用字元 `*.blob.core.windows.net` 或相關聯的 IP 位址範圍，來存取所有的 Azure blob 儲存體帳戶。
> [!NOTE]
> Azure Container Registry 引進[專用的資料端點](#enable-dedicated-data-endpoints)，可讓您嚴格限定登錄儲存體的用戶端防火牆規則。 使用 `<registry-name>.<region>.data.azurecr.io` 格式，選擇性在登錄所在或複寫的所有區域中啟用資料端點。

## <a name="allow-access-by-ip-address-range"></a>依據 IP 位址範圍允許存取

如果您的組織有僅允許存取特定 IP 位址或位址範圍的原則，請下載 [Azure IP 範圍和服務標記 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。

若要尋找您需要允許存取的 ACR REST 端點 IP 範圍，請在 JSON 檔案中搜尋 **AzureContainerRegistry**。

> [!IMPORTANT]
> Azure 服務的 IP 位址範圍會變動，每週會發佈更新。 請定期下載 JSON 檔案，並在您的存取規則中進行必要的更新。 如果您的案例牽涉到在 Azure 虛擬網路中設定網路安全性群組規則，或您是使用 Azure 防火牆，請改用 **AzureContainerRegistry** [服務標記](#allow-access-by-service-tag)。
>

### <a name="rest-ip-addresses-for-all-regions"></a>所有區域的 REST IP 位址

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>特定區域的 REST IP 位址

搜尋特定的區域，例如 **AzureContainerRegistry.AustraliaEast**。

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>所有區域的儲存體 IP 位址

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>特定區域的儲存體 IP 位址

搜尋特定區域，例如 **Storage.AustraliaCentral**。

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>依據服務標記允許存取

在 Azure 虛擬網路中，使用網路安全性規則來篩選從資源 (例如虛擬機器) 至容器登錄的流量。 為了簡化 Azure 網路規則的建立，請使用 **AzureContainerRegistry** [服務標記](../virtual-network/security-overview.md#service-tags)。 服務標記代表一組 IP 位址首碼，用於存取全球或個別 Azure 區域的 Azure 服務。 當位址變更時，標記會自動更新。 

例如，建立目的地 **AzureContainerRegistry** 的輸出網路安全性群組規則，來允許 Azure 容器登錄的流量。 若只要在特定區域中允許存取服務標記，請以下列格式指定區域：**AzureContainerRegistry**.[區域名稱]。

## <a name="enable-dedicated-data-endpoints"></a>啟用專用的資料端點 

> [!WARNING]
> 如果您先前設定讓用戶端防火牆可存取現有 `*.blob.core.windows.net` 端點，切換到專用的資料端點將會影響用戶端連線能力，因而導致提取失敗。 若要確保用戶端具有一致的存取權，請將新的資料端點規則新增至用戶端防火牆規則。 完成後，使用 Azure CLI 或其他工具，為您的登錄啟用專用的資料端點。

專用的資料端點是 **Premium** 容器登錄服務層級的選用功能。 如需登錄服務層級和限制的相關資訊，請參閱 [Azure Container Registry 服務層級](container-registry-skus.md)。 

您可以使用 Azure 入口網站或 Azure CLI 來啟用專用的資料端點。 資料端點會遵守區域模式 `<registry-name>.<region>.data.azurecr.io`。 在異地複寫的登錄中，啟用資料端點會啟用所有複本區域中的端點。

### <a name="portal"></a>入口網站

若要使用入口網站啟用資料端點：

1. 瀏覽至您的容器登錄。
1. 選取 [網路]  >  [公用存取]。
1. 選取 [啟用專用的資料端點] 核取方塊。
1. 選取 [儲存]。

資料端點 (一或多個) 便會出現在入口網站中。

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="入口網站中的專用資料端點":::

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 來啟用資料端點，請使用 Azure CLI 2.4.0 版或更高版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

下列 [az acr update][az-acr-update] 命令會在 *myregistry* 登錄上啟用專用的資料端點。 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

若要檢視資料端點，使用 [az acr show-endpoints][az-acr-show-endpoints] 命令：

```azurecli
az acr show-endpoints --name myregistry
```

以下示範用途的輸出中顯示兩個區域端點。

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

為您的登錄設定專用資料端點之後，您可以為資料端點啟用用戶端防火牆存取規則。 請為所有必要的登錄區域啟用資料端點存取規則。

## <a name="configure-client-firewall-rules-for-mcr"></a>設定 MCR 的用戶端防火牆規則

如果您需要從防火牆後方存取 Microsoft Container Registry (MCR)，請參閱設定 [MCR 用戶端防火牆規則](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)的指引。 MCR 是所有 Microsoft 發佈的 Docker 映像 (例如 Windows Server 映像) 的主要登錄。

## <a name="next-steps"></a>後續步驟

* 了解[適用於網路安全性的 Azure 最佳做法](../security/fundamentals/network-best-practices.md)

* 了解 Azure 虛擬網路中的[安全性群組](../virtual-network/security-overview.md)

* 了解如何設定容器登錄的[私人連結](container-registry-private-link.md)

* 了解 Azure Container Registry [專用的資料端點](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints
