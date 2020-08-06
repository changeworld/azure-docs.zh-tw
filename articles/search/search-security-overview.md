---
title: 安全性概觀
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋符合 SOC 2、HIPAA 和其他認證的規範。 連接和資料加密、驗證，以及透過篩選條件運算式中的使用者和群組安全識別碼來存取身分識別。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: fb265f8a8ab34972dac8529d267e41edaf0acb4c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829283"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Azure 認知搜尋中的安全性-總覽

本文說明 Azure 認知搜尋中可保護內容和作業的主要安全性功能。

+ 在儲存層中，會針對儲存到磁片的所有服務管理內容內建待用加密，包括索引、同義字對應，以及索引子、資料來源和技能集的定義。 Azure 認知搜尋也支援加入客戶管理的金鑰， (CMK) 以進行索引內容的補充加密。 針對 1 2020 年8月之後所建立的服務，CMK 加密會延伸到暫存磁片上的資料，以進行索引內容的完整雙重加密。

+ 輸入安全性可保護搜尋服務端點的安全性層級：從要求的 API 金鑰，到防火牆中的輸入規則，到從公用網際網路完全防護服務的私人端點。

+ 輸出安全性適用于從外部來源提取內容的索引子。 針對輸出要求，設定受控識別，以便在存取來自 Azure 儲存體、Azure SQL、Cosmos DB 或其他 Azure 資料來源的資料時，搜尋受信任的服務。 受控識別是用來替代認證或連接上的存取金鑰。 本文未涵蓋輸出安全性。 如需這項功能的詳細資訊，請參閱[使用受控識別連接到資料來源](search-howto-managed-identities-data-sources.md)。

觀賞這段快速的影片，以取得安全性架構和每個功能類別的總覽。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>加密的傳輸和儲存

在 Azure 認知搜尋中，加密會從連線和傳輸開始，並延伸至儲存在磁片上的內容。 針對公用網際網路上的搜尋服務，Azure 認知搜尋會接聽 HTTPS 埠443。 所有用戶端對服務連線都會使用 TLS 1.2 加密。 不支援舊版 (1.0 或 1.1) 。

針對搜尋服務內部處理的資料，下表描述[資料加密模型](../security/fundamentals/encryption-atrest.md#data-encryption-models)。 某些功能（例如知識存放區、累加擴充和以索引子為基礎的索引、讀取或寫入其他 Azure 服務中的資料結構）。 這些服務有自己的加密支援層級，與 Azure 認知搜尋不同。

| 模型 | 快速鍵&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 滿足&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 限制 | 適用於 |
|------------------|-------|-------------|--------------|------------|
| 伺服器端加密 | Microsoft 管理的金鑰 | 無 (內建)  | 無（適用于所有區域中的所有層），適用于 24 2018 年1月之後建立的內容。 | 內容 (索引和同義字對應) 和定義 (索引子、資料來源、技能集)  |
| 伺服器端加密 | 客戶管理的金鑰 | Azure 金鑰保存庫 | 適用于在2019年1月之後建立的可計費層（所有區域）。 | 資料磁片上) 的內容 (索引和同義字對應 |
| 伺服器端雙重加密 | 客戶管理的金鑰 | Azure 金鑰保存庫 | 適用于 1 2020 年8月之後搜尋服務的計費層，在所選區域中。 | 資料磁片和暫存磁片上) 的內容 (索引和同義字對應 |

### <a name="service-managed-keys"></a>服務管理的金鑰

服務管理的加密是一種 Microsoft 內部作業，根據[Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，使用256位的[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)。 它會自動出現在所有索引上，包括未完全加密 (在2018年1月) 之前建立之索引的累加式更新。

### <a name="customer-managed-keys-cmk"></a>客戶管理的金鑰 (CMK) 

客戶管理的金鑰需要額外的計費服務，Azure Key Vault，它可以在不同的區域，但在相同的訂用帳戶下，做為 Azure 認知搜尋。 啟用 CMK 加密將會增加索引大小，並降低查詢效能。 根據 date 的觀察值，您可以預期在查詢時間中會看到 30%-60% 的增加，雖然實際的效能會隨著索引定義和查詢類型而有所不同。 基於此效能的影響，建議您只在真正需要的索引上啟用這項功能。 如需詳細資訊，請參閱[在 Azure 認知搜尋中設定客戶管理的加密金鑰](search-security-manage-encryption-keys.md)。

<a name="double-encryption"></a>

### <a name="double-encryption"></a>雙重加密 

在 Azure 認知搜尋中，雙重加密是 CMK 的延伸模組。 它被視為兩個折迭的加密 (一次 CMK，再次由服務管理的金鑰) ，以及完整的範圍，包含寫入資料磁片的長期儲存體，以及寫入暫存磁片的短期儲存體。 1 2020 年8月之前和之後的 CMK，以及在 Azure 認知搜尋中讓 CMK 成為雙重加密功能的差異，是在暫存磁片上進行待用資料的額外加密。

目前在8月1日之後，于這些區域中建立的新服務提供雙重加密：

+ 美國西部 2
+ 美國東部
+ 美國中南部
+ US Gov 維吉尼亞州
+ US Gov 亞利桑那州

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>輸入安全性和端點保護

輸入安全性功能會透過增加的安全性和複雜性層級來保護搜尋服務端點。 首先，所有要求都需要 API 金鑰來進行已驗證的存取。 第二，您可以選擇性地設定防火牆規則，以限制對特定 IP 位址的存取。 針對先進保護，第三個選項是啟用 Azure 私人連結，以從所有網際網路流量保護您的服務端點。

### <a name="public-access-using-api-keys"></a>使用 API 金鑰的公用存取

根據預設，搜尋服務會透過公用雲端存取，使用以金鑰為基礎的驗證來進行系統管理或搜尋服務端點的查詢存取。 API 金鑰是由隨機產生的數字和字母所組成的字串。 金鑰的類型 (管理或查詢) 會決定存取層級。 提交有效的金鑰可證明要求源自受信任的實體。 

您的搜尋服務有兩個存取層級，由下列 API 金鑰啟用：

+ 系統管理金鑰 (允許在搜尋服務上進行[建立-讀取-更新-刪除](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)作業的讀寫存取權) 

+ 查詢金鑰 (允許對索引的檔集合進行唯讀存取) 

系統*管理金鑰*是在布建服務時建立的。 有兩個管理員金鑰，指定為主要** 和次要** 以將它們保持在各自的位置，但事實上，它們是可互換。 每個服務有兩個管理員金鑰，以便您在轉換其中一個時不會無法存取您的服務。 您可以根據 Azure 安全性最佳作法定期重新產生系統[管理金鑰](search-security-api-keys.md#regenerate-admin-keys)，但無法新增到總管理金鑰計數。 每個搜尋服務最多可有兩個系統管理金鑰。

*查詢金鑰*是視需要建立，而且是針對發出查詢的用戶端應用程式所設計。 您最多可以建立 50 個查詢金鑰。 在應用程式程式碼中，您可以指定搜尋 URL 和查詢 api 金鑰，以允許唯讀存取特定索引的檔集合。 端點、可供唯讀存取的 API 金鑰以及目標索引共同定義來自用戶端應用程式連接的範圍和存取層級。

針對每個要求都必須進行驗證，其中每個要求會由強制性金鑰、作業及物件所組成。 當兩個權限層級 (完整和唯讀) 加上內容 (例如，索引上的查詢作業) 鏈結在一起時，即足以在服務作業上提供全面的安全性。 如需關於金鑰的詳細資訊，請參閱[建立及管理 API 金鑰](search-security-api-keys.md)。

### <a name="ip-restricted-access"></a>受 IP 限制的存取

若要進一步控制搜尋服務的存取權，您可以建立輸入防火牆規則，以允許存取特定 IP 位址或 IP 位址範圍。 所有用戶端連線都必須透過允許的 IP 位址進行，否則連接會遭到拒絕。

您可以使用入口網站來[設定輸入存取](service-configure-firewall.md)。 

或者，您可以使用管理 REST Api。 API 版本2020-03-13，使用[IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule)參數，可讓您藉由識別您想要授與搜尋服務存取權的 IP 位址，以限制對服務的存取。 

### <a name="private-endpoint-no-internet-traffic"></a>私人端點 (沒有網際網路流量) 

Azure 認知搜尋的[私用端點](../private-link/private-endpoint-overview.md)可讓[虛擬網路](../virtual-network/virtual-networks-overview.md)上的用戶端透過[私人連結](../private-link/private-link-overview.md)，安全地存取搜尋索引中的資料。 

私人端點會使用虛擬網路位址空間中的 IP 位址，連接至您的搜尋服務。 用戶端與搜尋服務之間的網路流量會透過虛擬網路和 Microsoft 骨幹網路上的私人連結來進行，以消除公開網際網路的暴露。 VNET 可讓您透過內部部署網路和網際網路，在資源之間進行安全通訊。 

雖然這是最安全的解決方案，但使用其他服務是一項額外的成本，因此在深入探討之前，請確定您已清楚瞭解其優勢。 如需成本的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/private-link/)。 如需這些元件如何搭配使用的詳細資訊，請觀賞本文頂端的影片。 私用端點選項的涵蓋範圍從5:48 開始到影片中。 如需如何設定端點的指示，請參閱[建立 Azure 認知搜尋的私人端點](service-create-private-endpoint.md)。

## <a name="index-access"></a>索引存取權

在 Azure 認知搜尋中，個別索引不是安全物件。 相反地，索引的存取權是在服務層級決定， (服務) 的讀取或寫入存取權，以及作業的內容。

就使用者存取來說，您可以將查詢要求建構成使用查詢金鑰來進行連線 (這可讓所有查詢都變成唯讀)，並且包含應用程式所使用的特定索引。 在查詢要求中，並沒有聯結索引或同時存取多個索引的概念，所以所有要求都會依據定義以單一索引為目標。 因此，查詢要求本身的結構 (一個金鑰加上單一目標索引) 即可定義安全性界限。

系統管理員和開發人員對索引的存取權並無差別：兩者都需要有寫入存取權，才能建立、刪除及更新服務所管理的物件。 任何人只要擁有您服務的管理金鑰，便可讀取、修改或刪除該相同服務中的任何索引。 在防止發生意外或惡意刪除索引的情況方面，您內部對程式碼資產實施的來源控制，將會是反轉不需要之索引刪除或修改的補救措施。 Azure 認知搜尋會在叢集中進行容錯移轉以確保可用性，但不會儲存或執行用來建立或載入索引的專屬程式碼。

以需要索引層級之安全性界限的多組織用戶管理解決方案來說，這類解決方案通常會包含可供客戶用來處理索引隔離的中間層。 如需多租使用者使用案例的詳細資訊，請參閱多租使用者[SaaS 應用程式的設計模式和 Azure 認知搜尋](search-modeling-multitenant-saas-applications.md)。

## <a name="user-access"></a>使用者存取

使用者存取索引和其他物件的方式取決於要求上的 API 金鑰類型。 大部分的開發人員會為用戶端搜尋要求建立並指派[*查詢金鑰*](search-security-api-keys.md)。 查詢金鑰會授與唯讀存取權給索引中的可搜尋內容。

如果您需要細微的每一使用者控制搜尋結果，您可以在查詢上建立安全性篩選，並傳回與指定的安全性識別相關聯的檔。 以身分識別為基礎的存取控制，而不是預先定義的角色和角色指派，會實作為*篩選*條件，以根據識別來修剪檔和內容的搜尋結果。 下表說明兩個針對未經授權的內容縮減搜尋結果的方法。

| 方法 | 描述 |
|----------|-------------|
|[根據身分識別篩選進行安全性範圍縮減](search-security-trimming-for-azure-search.md)  | 記載實作使用者身分識別存取控制的基本工作流程。 其中涵蓋在索引中新增安全性識別碼，然後說明如何針對該欄位進行篩選來縮減所禁止內容的結果。 |
|[根據 Azure Active Directory 身分識別進行安全性範圍縮減](search-security-trimming-for-azure-search-with-aad.md)  | 本文是上一篇文章的延伸，提供從 Azure Active Directory (AAD) (Azure 雲端平台上的其中一項[免費服務](https://azure.microsoft.com/free/)) 擷取身分識別的步驟。 |

## <a name="administrative-rights"></a>系統管理許可權

Azure[角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)是建置於[Azure Resource Manager](../azure-resource-manager/management/overview.md)上的授權系統，可布建 Azure 資源。 在 Azure 認知搜尋中，會使用 Resource Manager 來建立或刪除服務、管理 API 金鑰，以及調整服務規模。 因此，不論是使用[入口網站](search-manage.md)、 [POWERSHELL](search-manage-powershell.md)或[管理 REST api](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)，Azure 角色指派都會決定誰可以執行這些工作。

相反地，在服務上裝載之內容的系統管理許可權（例如建立或刪除索引的能力）是透過 API 金鑰來授予，如[前一節](#index-access)中所述。

> [!TIP]
> 使用 Azure 範圍的機制，您可以鎖定訂用帳戶或資源，以防止使用者具有系統管理員許可權的意外或未經授權刪除您的搜尋服務。 如需詳細資訊，請參閱[鎖定資源以防止非預期的刪除](../azure-resource-manager/management/lock-resources.md)。

## <a name="certifications-and-compliance"></a>認證和合規性

Azure 認知搜尋已認證符合適用于公用雲端和 Azure Government 的多個全域、區域和業界特定標準。 如需完整清單，請從官方審查報告頁面下載[ **Microsoft Azure 合規性供應**專案白皮書](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)。

為了符合規範，您可以使用[Azure 原則](../governance/policy/overview.md)來執行[Azure 安全性基準測試](../security/benchmarks/introduction.md)的高安全性最佳作法。 Azure 安全性基準測試是安全性建議的集合，編碼到安全性控制，這些控制項對應至您應採取的重要動作，以減輕對服務和資料的威脅。 目前有11個安全性控制項，包括[網路安全性](../security/benchmarks/security-control-network-security.md)、[記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)，以及[資料保護](../security/benchmarks/security-control-data-protection.md)等等。

Azure 原則是 Azure 內建的功能，可協助您管理多項標準的合規性，包括 Azure 安全性基準測試的規範。 針對已知的基準測試，Azure 原則提供內建定義，其中提供兩個準則，以及可採取動作的回應來解決不符合規範的狀況。 

針對 Azure 認知搜尋，目前有一個內建定義。 這是用於診斷記錄。 使用此內建，您可以指派一個原則來識別遺失診斷記錄的任何搜尋服務，然後將其開啟。 如需詳細資訊，請參閱[Azure 原則 Azure 認知搜尋的法規合規性控制](security-controls-policy.md)。

## <a name="see-also"></a>另請參閱

+ [Azure 安全性基本概念](../security/fundamentals/index.yml)
+ [Azure 安全性](https://azure.microsoft.com/overview/security)
+ [Azure 資訊安全中心](../security-center/index.yml)