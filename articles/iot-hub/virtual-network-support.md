---
title: 對虛擬網路的 Azure IoT 中心支援
description: 如何使用 IoT 中心虛擬網路連接模式
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501430"
---
# <a name="iot-hub-support-for-virtual-networks"></a>對虛擬網路的 IoT 中心支援

本文介紹了 VNET 連接模式，並詳細介紹了如何通過客戶擁有的 Azure VNET 將專用連線體驗設置為 IoT 中心。

> [!NOTE]
> 本文中描述的 IoT 中心功能目前可用於[以下區域使用託管服務標識創建的](#create-an-iot-hub-with-managed-service-identity)IoT 中心：美國東部、美國中南部和美國西部 2。


## <a name="introduction"></a>簡介

預設情況下，IoT 中心主機名稱映射到具有 Internet 公開可路由 IP 位址的公共終結點。 如下圖所示，此 IoT 中心公共終結點由不同客戶擁有的集線器共用，IoT 設備可通過廣域網路絡和本地網路訪問。

幾個 IoT 中心功能（包括[消息路由](./iot-hub-devguide-messages-d2c.md)、[檔上載](./iot-hub-devguide-file-upload.md)和[批量設備導入/匯出](./iot-hub-bulk-identity-mgmt.md)）同樣需要通過其公共終結點從 IoT 中心連接到客戶擁有的 Azure 資源。 如下圖所示，這些連接路徑共同構成了從 IoT 中心到客戶資源的出口流量。
![IoT 中心公共終結點](./media/virtual-network-support/public-endpoint.png)


出於多種原因，客戶可能希望通過他們擁有和運營的 VNET 限制與其 Azure 資源（包括 IoT 中心）的連接。 其中包括：

* 通過網路級隔離為 IoT 中心引入其他安全層，防止通過公共 Internet 向集線器提供連線性暴露。

* 從本地網路資產中啟用專用連線體驗，確保資料和流量直接傳輸到 Azure 骨幹網。

* 防止來自敏感本地網路的滲漏攻擊。 

* 使用[專用終結點](../private-link/private-endpoint-overview.md)遵循已建立的 Azure 範圍連接模式。


本文介紹如何使用[專用終結點](../private-link/private-endpoint-overview.md)實現與 IoT 中心的入口連接，例如使用 Azure 受信任的第一方服務例外從 IoT 中心到其他 Azure 資源進行出口連接。


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>使用專用終結點進入 IoT 中心

專用終結點是在客戶擁有的 VNET 中分配的私人 IP 位址，可通過該位址到達 Azure 資源。 通過為 IoT 中心設置專用終結點，您將能夠允許在 VNET 內運行的服務到達 IoT 中心，而無需將流量發送到 IoT 中心的公共終結點。 同樣，在本地運行的設備可以使用[虛擬私人網路絡 （VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)專用對等互連來獲得與 Azure 中的 VNET 的連接，然後連接到 IoT 中心（通過其專用終結點）。 因此，希望限制與 IoT 中心公共終結點的連接（或可能完全阻止它）的客戶可以通過使用[IoT Hub 防火牆規則](./iot-hub-ip-filtering.md)來實現此目標，同時使用專用終結點保留與其中心的連接。

> [!NOTE]
> 此設置的主要重點是本地網路內的設備。 對於部署在廣域網路絡中的設備，不建議進行此設置。

![IoT 中心公共終結點](./media/virtual-network-support/virtual-network-ingress.png)

在繼續之前，請確保滿足以下先決條件：

* 您的 IoT 中心必須預配[託管服務標識](#create-an-iot-hub-with-managed-service-identity)。

* 您的 IoT 中心必須預配到[受支援的區域](#regional-availability-private-endpoints)之一。

* 您已預配 Azure VNET 與將在其中創建專用終結點的子網。 有關詳細資訊[，請參閱使用 Azure CLI 創建虛擬網路](../virtual-network/quick-create-cli.md)。

* 對於在本地網路內運行的設備，設置[虛擬私人網路絡 （VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或到 Azure VNET 的[專用](https://azure.microsoft.com/services/expressroute/)對等互連。


### <a name="regional-availability-private-endpoints"></a>區域可用性（專用終結點）

在以下區域中創建的 IoT 中心中支援的私人終結點：

* 美國東部

* 美國中南部

* 美國西部 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>為 IoT 中心入口設置專用終結點

要設置專用終結點，請按照以下步驟操作：

1. 運行以下 Azure CLI 命令以使用訂閱重新註冊 Azure IoT 中心提供程式：

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. 導航到 IoT 中心門戶中的 **"專用終結點連接**"選項卡（此選項卡僅在[受支援區域](#regional-availability-private-endpoints)中的 IoT 中心中可用），然後按一下**+** 該符號添加新專用終結點。

3. 提供訂閱、資源組、名稱和區域以在 中創建新的專用終結點（理想情況下，私有終結點應在與中心相同的區域中創建;有關詳細資訊，請參閱[區域可用性部分](#regional-availability-private-endpoints)）。

4. 按一下 **"下一步：資源**"，並為 IoT 中心資源提供訂閱，然後選擇 **"Microsoft.Devices/IotHubs"** 作為資源類型，選擇 IoT 中心名稱作為**資源**，**將 iotHub**作為目標子資源。

5. 按一下"**下一步：配置**"並提供虛擬網路和子網以在 中創建專用終結點。 如果需要，請選擇要與 Azure 專用 DNS 區域集成的選項。

6. 按一下 **"下一步："標記**"，並可選擇為資源提供任何標記。

7. 按一下"**審閱 + 創建**"以創建專用終結點資源。


### <a name="pricing-private-endpoints"></a>定價（專用終結點）

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>從 IoT 中心到其他 Azure 資源的出口連接

IoT 中心需要訪問 Azure Blob 存儲、事件中心、[消息路由](./iot-hub-devguide-messages-d2c.md)的服務匯流排資源、[檔上載](./iot-hub-devguide-file-upload.md)和[批量設備導入/匯出](./iot-hub-bulk-identity-mgmt.md)，這通常發生在資源的公共終結點上。 如果將存儲帳戶、事件中心或服務匯流排資源綁定到 VNET，則建議的配置將阻止預設情況下與該資源的連接。 因此，這將妨礙 IoT 中心需要訪問這些資源的功能。

為了緩解這種情況，您需要通過**Azure 第一方受信任服務**選項啟用從 IoT 中心資源到存儲帳戶、事件中心或服務匯流排資源的連接。

先決條件如下：

* 您的 IoT 中心必須預配到[受支援的區域](#regional-availability-trusted-microsoft-first-party-services)之一。

* 必須在中心預配時為 IoT 中心分配託管服務標識。 按照有關如何[創建具有託管服務標識的集線器的說明](#create-an-iot-hub-with-managed-service-identity)。


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>區域可用性（受信任的 Microsoft 第一方服務）

Azure 受信任的第一方服務例外，以繞過對 Azure 存儲、事件中心和服務匯流排資源的防火牆限制，僅在以下區域中支援 IoT 中心：

* 美國東部

* 美國中南部

* 美國西部 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>定價（受信任的 Microsoft 第一方服務）

受信任的 Microsoft 第一方服務例外功能在[受支援區域](#regional-availability-trusted-microsoft-first-party-services)中的 IoT 中心是免費的。 預配存儲帳戶、事件中心或服務匯流排資源的費用單獨適用。


### <a name="create-an-iot-hub-with-managed-service-identity"></a>使用託管服務標識創建 IoT 中心

託管服務標識可以在資源調配時間分配給您的中心（現有中心當前不支援此功能），這需要 IoT 中心使用 TLS 1.2 作為最小版本。 為此，您需要使用下面的 ARM 資源範本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

替換資源`name`的值 後，`location``SKU.name`和`SKU.tier`， 可以使用 Azure CLI 使用：

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

創建資源後，可以使用 Azure CLI 檢索分配給中心的託管服務標識：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

預配具有託管服務標識的 IoT 中心後，請按照相應的部分將路由終結點設置為[存儲帳戶](#egress-connectivity-to-storage-account-endpoints-for-routing)、[事件中心](#egress-connectivity-to-event-hubs-endpoints-for-routing)[和服務匯流排](#egress-connectivity-to-service-bus-endpoints-for-routing)資源，或配置[檔上載](#egress-connectivity-to-storage-accounts-for-file-upload)和[批量設備導入/匯出](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)。


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>與存儲帳戶終結點的連接進行路由

可以配置 IoT 中心將消息路由到客戶擁有的存儲帳戶。 要允許路由功能在防火牆限制到位時訪問存儲帳戶，IoT 中心需要具有託管服務標識（請參閱如何[創建具有託管服務標識的集線器](#create-an-iot-hub-with-managed-service-identity)）。 預配託管服務標識後，按照以下步驟向中心的資源標識授予 RBAC 訪問存儲帳戶的許可權。

1. 在 Azure 門戶中，導航到存儲帳戶的訪問**控制 （IAM）** 選項卡，然後按一下"**添加角色指派**"部分下的 **"添加**"。

2. 選擇**存儲 Blob 資料參與者**作為**角色****、Azure AD 使用者、組或服務主體****，以分配對**IoT 中心的資源名稱的存取權限，並在下拉清單中選擇 IoT 中心的資源名稱。 按一下 [儲存]**** 按鈕。

3. 導航到存儲帳戶中的 **"防火牆"和"虛擬網路**"選項卡，並啟用 **"允許從選定的網路訪問**"選項。 在 **"例外"** 清單下，選中 **"允許受信任的 Microsoft 服務訪問此存儲帳戶**"核取方塊。 按一下 [儲存]**** 按鈕。

4. 在 IoT 中心的資源頁面上，導航到**消息路由**選項卡。

5. 導航到**自訂終結點**部分，然後按一下"**添加**"。 選擇 **"存儲**"作為終結點類型。

6. 在顯示的頁面上，為終結點提供名稱，選擇要在 Blob 存儲中使用容器、提供編碼和檔案名格式。 選擇"為存儲終結點的**身份驗證類型****分配的系統**"。 按一下 [建立]**** 按鈕。

現在，自訂存儲終結點設置為使用集線器的系統分配標識，並且儘管它具有防火牆限制，但它仍有權訪問存儲資源。 現在，您可以使用此終結點設置路由規則。


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>到事件中心終結點進行路由的入侵連接

可以配置 IoT 中心將消息路由到客戶擁有的事件中心命名空間。 要允許路由功能在防火牆限制到位時訪問事件中心資源，IoT 中心需要具有託管服務標識（請參閱如何[創建具有託管服務標識的集線器](#create-an-iot-hub-with-managed-service-identity)）。 預配託管服務標識後，按照以下步驟向中心的資源標識授予 RBAC 訪問事件中心的許可權。

1. 在 Azure 門戶中，導航到事件中心**存取控制 （IAM）** 選項卡，然後按一下"**添加角色指派**"部分下的 **"添加**"。

2. 選擇**事件中心資料發送方**作為**角色****、Azure AD 使用者、組或服務主體**，**以分配對**IoT 中心的資源名稱的存取權限，並在下拉清單中選擇 IoT 中心的資源名稱。 按一下 [儲存]**** 按鈕。

3. 導航到事件中心中的 **"防火牆"和"虛擬網路**"選項卡，並啟用 **"允許從選定的網路訪問**"選項。 在 **"例外"** 清單下，選中"**允許受信任的 Microsoft 服務訪問事件中心**"核取方塊。 按一下 [儲存]**** 按鈕。

4. 在 IoT 中心的資源頁面上，導航到**消息路由**選項卡。

5. 導航到**自訂終結點**部分，然後按一下"**添加**"。 選擇**事件中心**作為終結點類型。

6. 在顯示的頁面上，為終結點提供名稱，選擇事件中心命名空間和實例，然後按一下"**創建**"按鈕。

現在，自訂事件中心終結點設置為使用集線器的系統分配的標識，並且儘管它具有防火牆限制，它仍有權訪問事件中心資源。 現在，您可以使用此終結點設置路由規則。


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>與用於路由的服務匯流排終結點的進出連接

可以配置 IoT 中心將消息路由到客戶擁有的服務匯流排命名空間。 要允許路由功能在防火牆限制到位時訪問服務匯流排資源，IoT 中心需要具有託管服務標識（請參閱如何[創建具有託管服務標識的集線器](#create-an-iot-hub-with-managed-service-identity)）。 預配託管服務標識後，按照以下步驟向中心的資源標識授予 RBAC 訪問服務匯流排的許可權。

1. 在 Azure 門戶中，導航到服務匯流排的訪問**控制 （IAM）** 選項卡，然後按一下"**添加角色指派**"部分下的 **"添加**"。

2. 選擇**服務匯流排資料發送器**作為**角色****、Azure AD 使用者、組或服務主體****，以分配對**IoT 中心的資源名稱的存取權限，並在下拉清單中選擇 IoT 中心的資源名稱。 按一下 [儲存]**** 按鈕。

3. 導航到服務匯流排中的**防火牆和虛擬網路**選項卡，並啟用 **"允許從選定的網路訪問**"選項。 在 **"例外"** 清單下，選中 **"允許受信任的 Microsoft 服務訪問此服務匯流排**"核取方塊。 按一下 [儲存]**** 按鈕。

4. 在 IoT 中心的資源頁面上，導航到**消息路由**選項卡。

5. 導航到**自訂終結點**部分，然後按一下"**添加**"。 選擇**服務匯流排佇列**或服務**匯流排主題**（如適用）作為終結點類型。

6. 在顯示的頁面上，為終結點提供名稱，選擇服務匯流排的命名空間和佇列或主題（如適用）。 按一下 [建立]**** 按鈕。

現在，自訂服務匯流排終結點設置為使用集線器的系統分配的標識，並且儘管它具有防火牆限制，它仍有權訪問服務匯流排資源。 現在，您可以使用此終結點設置路由規則。


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>檔上載與存儲帳戶的入侵連接

IoT 中心的檔上傳功能允許設備將檔上載到客戶擁有的存儲帳戶。 為了允許檔上載工作，設備和 IoT 中心都需要連接到存儲帳戶。 如果存儲帳戶上設置了防火牆限制，您的設備需要使用任何受支援的存儲帳戶機制（包括[專用終結點](../private-link/create-private-endpoint-storage-portal.md)、[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火牆配置](../storage/common/storage-network-security.md)）來獲得連接。 同樣，如果存儲帳戶上設置了防火牆限制，則需要將 IoT 中心配置為通過受信任的 Microsoft 服務異常訪問存儲資源。 為此，IoT 中心必須具有託管服務標識（請參閱如何[創建具有託管服務標識的中心](#create-an-iot-hub-with-managed-service-identity)）。 預配託管服務標識後，按照以下步驟向中心的資源標識授予 RBAC 訪問存儲帳戶的許可權。

1. 在 Azure 門戶中，導航到存儲帳戶的訪問**控制 （IAM）** 選項卡，然後按一下"**添加角色指派**"部分下的 **"添加**"。

2. 選擇**存儲 Blob 資料參與者**作為**角色****、Azure AD 使用者、組或服務主體****，以分配對**IoT 中心的資源名稱的存取權限，並在下拉清單中選擇 IoT 中心的資源名稱。 按一下 [儲存]**** 按鈕。

3. 導航到存儲帳戶中的 **"防火牆"和"虛擬網路**"選項卡，並啟用 **"允許從選定的網路訪問**"選項。 在 **"例外"** 清單下，選中 **"允許受信任的 Microsoft 服務訪問此存儲帳戶**"核取方塊。 按一下 [儲存]**** 按鈕。

4. 在 IoT 中心的資源頁面上，導航到**檔上載**選項卡。

5. 在顯示的頁面上，選擇要在 blob 存儲中使用的容器，根據需要配置**檔通知設置****、SAS TTL、****預設 TTL**和**最大傳遞計數**。 選擇"為存儲終結點的**身份驗證類型****分配的系統**"。 按一下 [建立]**** 按鈕。

現在，檔上載的存儲終結點設置為使用集線器的系統分配的標識，並且儘管它具有防火牆限制，它仍有權訪問存儲資源。


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>批量設備導入/匯出的與存儲帳戶的入侵連接

IoT 中心支援從/到客戶提供的存儲 Blob 批量[導入/匯出](./iot-hub-bulk-identity-mgmt.md)設備資訊的功能。 為了允許大量匯入/匯出功能正常工作，設備和 IoT 中心都需要連接到存儲帳戶。

此功能需要從 IoT 中心連接到存儲帳戶。 要在防火牆限制到位時訪問服務匯流排資源，IoT 中心需要具有託管服務標識（請參閱如何[創建具有託管服務標識的集線器](#create-an-iot-hub-with-managed-service-identity)）。 預配託管服務標識後，按照以下步驟向中心的資源標識授予 RBAC 訪問服務匯流排的許可權。

1. 在 Azure 門戶中，導航到存儲帳戶的訪問**控制 （IAM）** 選項卡，然後按一下"**添加角色指派**"部分下的 **"添加**"。

2. 選擇**存儲 Blob 資料參與者**作為**角色****、Azure AD 使用者、組或服務主體****，以分配對**IoT 中心的資源名稱的存取權限，並在下拉清單中選擇 IoT 中心的資源名稱。 按一下 [儲存]**** 按鈕。

3. 導航到存儲帳戶中的 **"防火牆"和"虛擬網路**"選項卡，並啟用 **"允許從選定的網路訪問**"選項。 在 **"例外"** 清單下，選中 **"允許受信任的 Microsoft 服務訪問此存儲帳戶**"核取方塊。 按一下 [儲存]**** 按鈕。

現在，可以使用 Azure IoT REST API[創建導入匯出作業](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs)，以獲取有關如何使用大量匯入/匯出功能的資訊。 請注意，您需要分別在請求正文`storageAuthenticationType="identityBased"`中提供 ，`inputBlobContainerUri="https://..."`並`outputBlobContainerUri="https://..."`分別用作存儲帳戶的輸入和輸出 URL。


Azure IoT 中心 SDK 在服務用戶端的註冊表管理器中也支援此功能。 以下程式碼片段演示如何使用 C# SDK 啟動導入作業或匯出作業。

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


要使用此受區域限制的 Azure IoT SDK 版本，支援對 C#、JAVA 和 Node.js 的虛擬網路支援：

1. 創建名為`EnableStorageIdentity`的環境變數並將其值設置為`1`。

2. 下載 SDK：JAVA [ ](https://aka.ms/vnetjavasdk)  |  [C++](https://aka.ms/vnetcsharpsdk) | [節點.js](https://aka.ms/vnetnodesdk)
 
對於 Python，請從 GitHub 下載我們的有限版本。

1. 導航到[GitHub 發佈頁](https://aka.ms/vnetpythonsdk)。

2. 下載以下檔，您可以在標題下名為 **"資產**"的發佈頁面底部找到該檔。
    > *azure_iot_hub-2.2.0_limited-py2.py3-無-任何.whl*

3. 打開終端，使用下載的檔導航到資料夾。

4. 運行以下命令以安裝支援虛擬網路的 Python 服務 SDK：
    > 點安裝 ./azure_iot_hub-2.2.0_limited-py2.py3-無-任何.whl


## <a name="next-steps"></a>後續步驟

使用以下連結瞭解有關 IoT 中心功能的更多內容：

* [訊息路由](./iot-hub-devguide-messages-d2c.md)
* [檔案上傳](./iot-hub-devguide-file-upload.md)
* [批量設備導入/匯出](./iot-hub-bulk-identity-mgmt.md) 
