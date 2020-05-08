---
title: 防火牆存取規則
description: 設定規則以從防火牆後方存取 Azure container registry，方法是允許存取（"允許清單"） REST API 和資料端點功能變數名稱或服務特定的 IP 位址範圍。
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: b3560fe769a97c8d3a4e5a3580d42d7c0b3a3f08
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978343"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>設定規則以存取防火牆後方的 Azure container registry

本文說明如何在防火牆上設定規則，以允許存取 Azure container registry。 例如，位於防火牆或 proxy 伺服器後方的 Azure IoT Edge 裝置可能需要存取容器登錄來提取容器映射。 或者，在內部部署網路中鎖定的伺服器可能需要存取以推送映射。

如果您想要改為在 Azure 虛擬網路內設定對容器登錄的輸入網路存取，請參閱[設定 azure container registry 的 Azure 私人連結](container-registry-private-link.md)。

## <a name="about-registry-endpoints"></a>關於登錄端點

若要將映射或其他成品提取或推送至 Azure container registry，用戶端（例如 Docker daemon）必須透過 HTTPS 與兩個不同的端點進行互動。 對於從防火牆後方存取登錄的用戶端，您必須設定這兩個端點的存取規則。

* 登錄**REST API 端點**-驗證和登錄管理作業會透過登錄的公用 REST API 端點來處理。 此端點是登錄的登入伺服器名稱。 範例：`myregistry.azurecr.io`

* **儲存體（資料）端點**-Azure 會代表每個登錄在 Azure 儲存體帳戶中配置[blob 儲存體](container-registry-storage.md)，以管理容器映射和其他成品的資料。 當用戶端存取 Azure container registry 中的映射層時，它會使用登錄所提供的儲存體帳戶端點來提出要求。

如果您的登錄是[異地](container-registry-geo-replication.md)複寫的，用戶端可能需要與特定區域或多個複寫區域中的資料端點互動。

## <a name="allow-access-to-rest-and-data-endpoints"></a>允許存取 REST 和資料端點

* **REST 端點**-允許存取完整登錄登入伺服器名稱、 `<registry-name>.azurecr.io`或相關聯的 IP 位址範圍
* **儲存體（資料）端點**-允許使用萬用字元`*.blob.core.windows.net`或相關聯的 IP 位址範圍來存取所有 Azure blob 儲存體帳戶。
> [!NOTE]
> Azure Container Registry 引進[專用的資料端點](#enable-dedicated-data-endpoints-preview)（預覽），可讓您嚴格限定登錄儲存體的用戶端防火牆規則。 （選擇性）使用格式`<registry-name>.<region>.data.azurecr.io`，在登錄所在的所有區域中啟用資料端點。

## <a name="allow-access-by-ip-address-range"></a>允許依 IP 位址範圍存取

如果您的組織有僅允許存取特定 IP 位址或位址範圍的原則，請下載[AZURE IP 範圍和服務標籤–公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。

若要尋找您需要允許存取的 ACR REST 端點 IP 範圍，請在 JSON 檔案中搜尋**AzureContainerRegistry** 。

> [!IMPORTANT]
> Azure 服務的 IP 位址範圍可能會變更，並每週發佈更新。 定期下載 JSON 檔案，並在您的存取規則中進行必要的更新。 如果您的案例牽涉到在 Azure 虛擬網路中設定網路安全性群組規則，或使用 Azure 防火牆，請改用**AzureContainerRegistry** [服務標記](#allow-access-by-service-tag)。
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

搜尋特定的區域，例如**AzureContainerRegistry. AustraliaEast**。

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

搜尋特定的區域，例如**AustraliaCentral**。

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

## <a name="allow-access-by-service-tag"></a>允許依服務標記存取

在 Azure 虛擬網路中，使用網路安全性規則來篩選來自資源（例如虛擬機器）至容器登錄的流量。 若要簡化 Azure 網路規則的建立，請使用**AzureContainerRegistry** [服務標記](../virtual-network/security-overview.md#service-tags)。 服務標籤代表一組 IP 位址首碼，可存取全域或每個 Azure 區域的 Azure 服務。 當地址變更時，會自動更新標記。 

例如，建立具有目的地**AzureContainerRegistry**的輸出網路安全性群組規則，以允許 Azure container registry 的流量。 若只要在特定區域中允許存取服務標記，請以下列格式指定區域： **AzureContainerRegistry**。[*區功能變數名稱稱*]。

## <a name="enable-dedicated-data-endpoints-preview"></a>啟用專用的資料端點（預覽）

> [!WARNING]
> 如果您先前設定了現有`*.blob.core.windows.net`端點的用戶端防火牆存取，切換到專用的資料端點將會影響用戶端連線能力，因而導致提取失敗。 若要確保用戶端具有一致的存取權，請將新的資料端點規則新增至用戶端防火牆規則。 完成後，使用 Azure CLI 或其他工具，為您的登錄啟用專用的資料端點。

專用的資料端點是**高階 container registry**服務層級的選用功能。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry 層](container-registry-skus.md)。 若要使用 Azure CLI 來啟用資料端點，請使用 Azure CLI version 2.4.0 或更高版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

下列[az acr update][az-acr-update]命令會在登錄*myregistry*上啟用專用的資料端點。 基於示範目的，假設登錄是在兩個區域中複寫：

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

資料端點會使用區域模式`<registry-name>.<region>.data.azurecr.io`。 若要查看資料端點，請使用[az acr show-端點][az-acr-show-endpoints]命令：

```azurecli
az acr show-endpoints --name myregistry
```

輸出：

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

設定登錄的專用資料端點之後，您可以為資料端點啟用用戶端防火牆存取規則。 針對所有必要的登錄區域啟用資料端點存取規則。

## <a name="configure-client-firewall-rules-for-mcr"></a>設定 MCR 的用戶端防火牆規則

如果您需要從防火牆後方存取 Microsoft Container Registry （MCR），請參閱設定[MCR 用戶端防火牆規則](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)的指引。 MCR 是所有 Microsoft 發佈的 docker 映射（例如 Windows Server 映射）的主要登錄。

## <a name="next-steps"></a>後續步驟

* 瞭解[適用于網路安全性的 Azure 最佳作法](../security/fundamentals/network-best-practices.md)

* 深入瞭解 Azure 虛擬網路中的[安全性群組](/azure/virtual-network/security-overview)

* 深入瞭解 Azure Container Registry[專用的資料端點](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

