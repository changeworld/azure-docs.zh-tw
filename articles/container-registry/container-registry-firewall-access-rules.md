---
title: 防火牆訪問規則
description: 通過允許訪問（"白名單"）REST API 和存儲終結點功能變數名稱或服務特定的 IP 位址範圍，配置規則以從防火牆後面訪問 Azure 容器註冊表。
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168009"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>配置規則以訪問防火牆後面的 Azure 容器註冊表

本文介紹如何在防火牆上配置規則以允許訪問 Azure 容器註冊表。 例如，防火牆或代理伺服器後面的 Azure IoT Edge 設備可能需要訪問容器註冊表才能提取容器映射。 或者，本地網路中的鎖定伺服器可能需要存取權限才能推送映射。

相反，如果只想在 Azure 虛擬網路或公共 IP 位址範圍內配置容器註冊表上的入站網路訪問規則，請參閱[限制從虛擬網路訪問 Azure 容器註冊表](container-registry-vnet.md)。

## <a name="about-registry-endpoints"></a>關於註冊表終結點

要將圖像或其他專案拉到 Azure 容器註冊表，需要使用兩個不同的終結點通過 HTTPS 進行交互的用戶端（如 Docker 守護進程）。

* **註冊表 REST API 終結點**- 身份驗證和註冊表管理操作通過註冊表的公共 REST API 終結點進行處理。 此終結點是註冊表的登錄伺服器名稱或關聯的 IP 位址範圍。 

* **存儲終結點**- Azure 代表每個註冊表在 Azure 存儲帳戶中[分配 Blob 存儲](container-registry-storage.md)，以管理容器映射和其他專案的資料。 當用戶端存取 Azure 容器註冊表中的映射層時，它將使用註冊表提供的存儲帳戶終結點發出請求。

如果您的註冊表是[異地複製的](container-registry-geo-replication.md)，則用戶端可能需要與特定區域或多個複製區域中的 REST 和存儲終結點進行交互。

## <a name="allow-access-to-rest-and-storage-domain-names"></a>允許訪問 REST 和存儲功能變數名稱

* **REST 終結點**- 允許訪問完全限定的註冊表登錄伺服器名稱，例如`myregistry.azurecr.io`
* **存儲（資料）終結點**- 允許使用萬用字元訪問所有 Azure Blob 存儲帳戶`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>允許按 IP 位址範圍訪問

如果您的組織具有僅允許訪問特定 IP 位址或位址範圍的策略，請下載[Azure IP 範圍和服務標記 - 公共雲](https://www.microsoft.com/download/details.aspx?id=56519)。

要查找需要允許訪問的 ACR REST 終結點 IP 範圍，請在 JSON 檔中搜索**Azure 容器註冊。**

> [!IMPORTANT]
> Azure 服務的 IP 位址範圍可以更改，並且每週發佈更新。 定期下載 JSON 檔，並在訪問規則中進行必要的更新。 如果方案涉及在 Azure 虛擬網路中配置網路安全性群組規則以訪問 Azure 容器註冊表，則改用**Azure 容器註冊**[服務標記](#allow-access-by-service-tag)。
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

搜索特定區域，如 Azure**容器註冊.澳大利亞東部**。

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

### <a name="storage-ip-addresses-for-all-regions"></a>存儲所有區域的 IP 位址

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

### <a name="storage-ip-addresses-for-specific-regions"></a>特定區域的存儲 IP 位址

搜索特定區域，如**存儲.澳大利亞中部**。

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

## <a name="allow-access-by-service-tag"></a>允許按服務標記訪問

在 Azure 虛擬網路中，使用網路安全規則將流量從資源（如虛擬機器）篩選到容器註冊表。 要簡化 Azure 網路規則的創建，請使用**Azure 容器註冊服務**[標記](../virtual-network/security-overview.md#service-tags)。 服務標記表示一組 IP 位址首碼，用於全域或每個 Azure 區域訪問 Azure 服務。 位址更改時，標記將自動更新。 

例如，創建具有目標**Azure 容器註冊表**的出站網路安全性群組規則，以允許流量到 Azure 容器註冊表。 要允許僅在特定區域中訪問服務標記，請以以下格式指定區域 **：Azure 容器註冊 。**[*區功能變數名稱稱*]

## <a name="configure-client-firewall-rules-for-mcr"></a>為 MCR 配置用戶端防火牆規則

如果需要從防火牆後面訪問 Microsoft 容器註冊表 （MCR），請參閱配置[MCR 用戶端防火牆規則的](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)指南。 MCR 是所有 Microsoft 發佈的 Docker 映射（如 Windows Server 映射）的主註冊表。

## <a name="next-steps"></a>後續步驟

* 瞭解[網路安全的 Azure 最佳實踐](../security/fundamentals/network-best-practices.md)

* 瞭解有關 Azure 虛擬網路中[安全性群組](/azure/virtual-network/security-overview)的更多資訊



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

