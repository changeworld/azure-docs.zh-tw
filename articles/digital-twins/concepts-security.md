---
title: Azure Digital Twins 解決方案的安全性
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 的安全性最佳作法。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bf92765431ea6b0f80b96ab7d61e8e830220dc82
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934540"
---
# <a name="secure-azure-digital-twins"></a>保護 Azure 數位 Twins

為了安全起見，Azure 數位 Twins 可讓您精確地控制部署中的特定資料、資源和動作。 它會透過稱為 **azure 角色型存取控制 (AZURE RBAC)** 的細微角色和版權管理策略來執行此工作。 您可以在 [這裡](../role-based-access-control/overview.md)閱讀 Azure RBAC 的一般原則。

Azure 數位 Twins 也支援靜態資料的加密。

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC 的角色和許可權

Azure RBAC 透過整合 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ，提供給 Azure 數位 Twins。

您可以使用 Azure RBAC 將許可權授與 *安全性主體，該安全性主體* 可能是使用者、群組或應用程式服務主體。 安全性主體是由 Azure AD 進行驗證，並接收傳回的 OAuth 2.0 權杖。 此權杖可以用來授權對 Azure 數位 Twins 實例的存取要求。

### <a name="authentication-and-authorization"></a>驗證與授權

使用 Azure AD，存取會有兩個步驟的程式。 當安全性主體 (使用者、群組或應用程式) 嘗試存取 Azure 數位 Twins 時，要求必須 *經過驗證* 和 *授權*。 

1. 首先，會驗證安全性主體的 *身分* 識別，並傳回 OAuth 2.0 權杖。
2. 接下來，權杖會作為要求的一部分傳遞至 Azure 數位 Twins 服務，以 *授權* 存取指定的資源。

驗證步驟需要在執行時間將任何應用程式要求包含 OAuth 2.0 存取權杖。 如果應用程式是在 Azure 實體中執行（例如 [Azure Functions](../azure-functions/functions-overview.md) 應用程式），則可以使用 **受控識別** 來存取資源。 在下一節中深入瞭解受控識別。

授權步驟會要求將 Azure 角色指派給安全性主體。 指派給安全性主體的角色會決定主體將擁有的許可權。 Azure 數位 Twins 提供的 Azure 角色包含 Azure 數位 Twins 資源的一組許可權。 本文稍後會說明這些角色。

若要深入瞭解 Azure 中支援的角色和角色指派，請參閱瞭解 Azure RBAC 檔中 [*的不同角色*](../role-based-access-control/rbac-and-directory-admin-roles.md) 。

#### <a name="authentication-with-managed-identities"></a>使用受控識別執行驗證作業

[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別是一種跨 Azure 功能，可讓您建立與應用程式程式碼執行所在之部署相關聯的安全身分識別。 然後，您可以將該身分識別與存取控制角色建立關聯，以授與自訂許可權來存取您應用程式所需的特定 Azure 資源。

使用受控識別，Azure 平台就能管理此執行階段識別。 您不需要為了識別本身或您需要存取的資源，在應用程式程式碼或設定中儲存及保護存取金鑰。 在 Azure App Service 應用程式內執行的 Azure 數位 Twins 用戶端應用程式不需要處理 SAS 規則和金鑰，或任何其他存取權杖。 用戶端應用程式只需要 Azure 數位 Twins 命名空間的端點位址。 當應用程式連線時，Azure 數位 Twins 會將受管理實體的內容系結至用戶端。 一旦與受控識別相關聯，您的 Azure 數位 Twins 用戶端就可以進行所有授權的作業。 然後將受管理的實體與 Azure 數位 Twins Azure 角色建立關聯，以授與授權， (如下) 所述。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>授權：適用于 Azure 數位 Twins 的 Azure 角色

Azure 提供 **兩個 azure 內建角色** ，以授權存取 Azure 數位 Twins [資料平面 api](how-to-use-apis-sdks.md#overview-data-plane-apis)。 您可以依名稱或識別碼來參考角色：

| 內建角色 | 描述 | ID | 
| --- | --- | --- |
| Azure 數位 Twins 資料擁有者 | 提供 Azure 數位 Twins 資源的完整存取權 | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure 數位 Twins 資料讀者 | 提供 Azure 數位 Twins 資源的唯讀存取權 | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

>[!NOTE]
> 這些角色最近在預覽版中已重新命名為先前的名稱：
> * *Azure Digital Twins 資料擁有者* 先前稱為 *Azure Digital Twins 擁有者 (預覽)* 。
> * *Azure 數位 Twins 資料讀取器* 先前為 *Azure 數位 Twins 讀者 (預覽版)*。

您可以透過兩種方式來指派角色：
* 透過 Azure 入口網站 (中 Azure 數位 Twins 的 [存取控制] (IAM) 窗格，請參閱 [*使用 Azure 入口網站新增或移除 azure 角色指派*](../role-based-access-control/role-assignments-portal.md)) 
* via CLI 命令以新增或移除角色

如需如何執行這項作業的詳細步驟，請在 Azure 數位 Twins 教學課程 [*：連接端對端解決方案*](tutorial-end-to-end.md)中試用。

如需如何定義內建角色的詳細資訊，請參閱瞭解 Azure RBAC 檔中的 [*角色定義*](../role-based-access-control/role-definitions.md) 。 如需建立 Azure 自訂角色的詳細資訊，請參閱 [*azure 自訂角色*](../role-based-access-control/custom-roles.md)。

##### <a name="automating-roles"></a>自動化角色

在自動化案例中參考角色時，建議您依 **識別碼** 來參考它們，而非其名稱。 版本之間的名稱可能會變更，但識別碼則不會變更，因此在自動化中會有更穩定的參考。

> [!TIP]
> 如果您要使用 Cmdlet 指派角色，例如 `New-AzRoleAssignment` ([參考](/powershell/module/az.resources/new-azroleassignment)) ，您可以使用 `-RoleDefinitionId` 參數（而非） `-RoleDefinitionName` 來傳遞識別碼，而不是角色的名稱。

### <a name="permission-scopes"></a>權限範圍

將 Azure 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定最好只授與最少的可能範圍。

下列清單說明您可以範圍存取 Azure 數位 Twins 資源的層級。
* 模型：此資源的動作會對在 Azure 數位 Twins 中上傳的 [模型](concepts-models.md) 進行控制。
* 查詢數位 Twins 圖：此資源的動作會決定在 Azure 數位 Twins 圖形內對數位 Twins 執行 [查詢作業](concepts-query-language.md) 的能力。
* 數位對應項：此資源的動作可讓您控制對應項圖形中 [數位 twins](concepts-twins-graph.md) 的 CRUD 作業。
* 數位對應項關聯性：此資源的動作會針對對應項圖形中數位 twins 之間的 [關聯](concepts-twins-graph.md) 性定義 CRUD 作業的控制權。
* 事件路由：此資源的動作會決定將事件從 Azure 數位 Twins [路由傳送](concepts-route-events.md) 至端點服務的許可權，例如 [事件中樞](../event-hubs/event-hubs-about.md)、 [事件方格](../event-grid/overview.md)或 [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)。

### <a name="troubleshooting-permissions"></a>疑難排解許可權

如果使用者嘗試執行其角色不允許的動作，他們可能會收到來自服務要求讀取的錯誤 `403 (Forbidden)` 。 如需詳細資訊和疑難排解步驟，請參閱 [*疑難排解： Azure 數位 Twins 要求失敗，狀態： 403 (禁止)*](troubleshoot-error-403.md)。

## <a name="managed-identity-for-accessing-other-resources-preview"></a>用來存取其他資源 (預覽) 的受控識別

設定 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) Azure 數位 Twins 實例的 **受控識別** ，可讓實例輕鬆地存取其他受 Azure AD 保護的資源，例如 [Azure Key Vault](../key-vault/general/overview.md)。 身分識別是由 Azure 平臺所管理，而且您不需要布建或輪替任何秘密。 如需 Azure AD 中受控識別的詳細資訊，請參閱  [*適用于 Azure 資源的受控*](../active-directory/managed-identities-azure-resources/overview.md)識別。 

Azure 支援兩種類型的受控識別：系統指派和使用者指派。 Azure 數位 Twins 目前僅支援 **系統指派的** 身分識別。 

您可以針對 Azure 數位實例使用系統指派的受控識別，向 [自訂定義的端點](concepts-route-events.md#create-an-endpoint)進行驗證。 Azure 數位 Twins 支援系統指派的身分識別型驗證給[事件中樞](../event-hubs/event-hubs-about.md)和 [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)   目的地的端點，以及針對寄[](../storage/blobs/storage-blobs-introduction.md)不出   [的信件事件](concepts-route-events.md#dead-letter-events)Azure 儲存體的容器端點。 [事件方格](../event-grid/overview.md)  受控識別目前不支援端點。

如需如何為 Azure 數位 Twins 啟用系統管理的身分識別，並使用它來路由事件的指示，請參閱作法 [*：啟用路由事件的受控識別 (預覽)*](how-to-enable-managed-identities.md)。

## <a name="private-network-access-with-azure-private-link-preview"></a>使用 Azure Private Link (preview 的私人網路存取) 

[Azure Private Link](../private-link/private-link-overview.md)是一項服務，可讓您透過[azure 虛擬網路 Azure Cosmos DB VNet) ](../virtual-network/virtual-networks-overview.md)中的私人端點，來存取 azure 資源 (例如[Azure 事件中樞](../event-hubs/event-hubs-about.md)、 [Azure 儲存體](../storage/common/storage-introduction.md)和[ (](../cosmos-db/introduction.md)) ，以及 azure 裝載的客戶和合作夥伴服務。 

同樣地，您可以使用 Azure 數位對應項實例的私人端點，以允許位於虛擬網路中的用戶端透過 Private Link 安全地存取實例。 

私人端點會使用來自您 Azure VNet 位址空間的 IP 位址。 您私人網路上的用戶端與 Azure 數位 Twins 實例之間的網路流量會透過 VNet 和 Microsoft 骨幹網路上的 Private Link 來進行，而不會暴露在公用網際網路上。 以下是此系統的視覺標記法：

:::image type="content" source="media/concepts-security/private-link.png" alt-text="此圖顯示 PowerGrid 公司的網路，此公司是沒有網際網路/公用雲端存取的受保護 VNET，透過 Private Link 連接到名為 CityOfTwins 的 Azure 數位 Twins 實例。":::

為您的 Azure 數位 Twins 實例設定私人端點，可讓您保護 Azure 數位 Twins 實例的安全，並消除公開風險，同時避免從您的 VNet 遭到外泄資料。

如需如何設定 Azure 數位 Twins Private Link 的指示，請參閱作法 [*：使用 Private Link 啟用私用存取 (preview)*](how-to-enable-private-link.md)。

### <a name="design-considerations"></a>設計考量 

使用適用于 Azure 數位 Twins 的 Private Link 時，以下是您可能想要考慮的一些因素：
* **定價**：如需定價詳細資料，請參閱  [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。 
* **區域可用性**：針對 Azure 數位 Twins，此功能可在所有 azure 區域提供 Azure 數位 Twins。 
* **每個 Azure 數位 Twins 實例的私人端點數目上限**：10

如需 Private Link 限制的詳細資訊，請參閱 [Azure Private Link 檔：限制](../private-link/private-link-service-overview.md#limitations)。

## <a name="service-tags"></a>服務標籤

**服務** 標籤代表來自指定 Azure 服務的一組 IP 位址首碼。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤，而盡可能簡化網路安全性規則頻繁的更新。 如需服務標記的詳細資訊，請參閱  [*虛擬網路標記*](../virtual-network/service-tags-overview.md)。 

您可以使用服務標籤來定義 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules)   或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制，方法是在建立安全性規則時使用服務標記來取代特定的 IP 位址。 藉由指定服務標籤名稱 (在此案例中， **AzureDigitalTwins**) 在規則的適當 *來源*   或 *目的地*   欄位中，您可以允許或拒絕對應服務的流量。 

以下是 **AzureDigitalTwins** 服務標記的詳細資料。

| Tag | 目的 | 可以使用輸入還是輸出？ | 是否可為區域性？ | 是否可與 Azure 防火牆搭配使用？ |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>注意：此標籤或此標記所涵蓋的 IP 位址，可用來限制對 [事件路由](concepts-route-events.md)設定之端點的存取。 | 輸入 | 否 | 是 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>使用服務標記來存取事件路由端點 

以下是使用服務標記搭配 Azure 數位 Twins 來存取 [事件路由](concepts-route-events.md) 端點的步驟。

1. 首先，下載此 JSON 檔案參考，其中顯示 Azure IP 範圍和服務標記： [*AZURE Ip 範圍和服務*](https://www.microsoft.com/download/details.aspx?id=56519)標籤。 

2. 在 JSON 檔案中尋找 "AzureDigitalTwins" IP 範圍。  

3. 請參閱連接到端點的外部資源的檔 (例如，[事件方格](../event-grid/overview.md)、[事件中樞](../event-hubs/event-hubs-about.md)、[服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)或寄不出[信件事件](concepts-route-events.md#dead-letter-events)的[Azure 儲存體](../storage/blobs/storage-blobs-overview.md)) 查看如何設定該資源的 IP 篩選器。

4. 使用 *步驟 2* 中的 ip 範圍，在外部資源 (s) 上設定 ip 篩選器。  

5. 視需要定期更新 IP 範圍。 範圍可能會隨著時間而改變，因此建議您定期檢查並視需要重新整理這些範圍。 這些更新的頻率可能不同，但建議您每週檢查一次。

## <a name="encryption-of-data-at-rest"></a>待用資料加密

Azure 數位 Twins 會將待用資料和傳輸中的資料加密提供給資料中心，並在您存取資料中心時將其解密。 這項加密會使用 Microsoft 管理的加密金鑰進行。

## <a name="cross-origin-resource-sharing-cors"></a>跨原始來源資源分享 (CORS)

Azure 數位 Twins 目前不支援 **跨原始來源資源分享 (CORS)**。 因此，如果您是從瀏覽器應用程式呼叫 REST API、 [API 管理 (APIM) ](../api-management/api-management-key-concepts.md) 介面或 [Power Apps](/powerapps/powerapps-overview) 連接器，您可能會看到原則錯誤。

若要解決這個錯誤，您可以執行下列其中一個動作：
* 從訊息中去除 CORS 標頭 `Access-Control-Allow-Origin` 。 此標頭會指出是否可以共用回應。 
* 或者，建立 CORS proxy 並透過它來 REST API 要求。 

## <a name="next-steps"></a>後續步驟

* 請參閱 [*如何：設定實例和驗證*](how-to-set-up-instance-portal.md)，以瞭解這些概念的作用。

* 請參閱 how to [*：撰寫應用程式驗證程式*](how-to-authenticate-client.md)代碼，以瞭解如何從用戶端應用程式程式碼與這些概念互動。

* 深入瞭解 [AZURE RBAC](../role-based-access-control/overview.md)。