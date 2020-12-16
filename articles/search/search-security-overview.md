---
title: 安全性概觀
titleSuffix: Azure Cognitive Search
description: 瞭解 Azure 認知搜尋中的安全性功能，以保護端點、內容和作業。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: references_regions
ms.openlocfilehash: ffb5a78c13413a46565a9c57c87dc8273742fd24
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563444"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Azure 認知搜尋中的安全性-總覽

本文說明 Azure 認知搜尋中可保護內容和作業的主要安全性功能。

+ 在儲存層中，會針對儲存至磁片的所有服務管理內容內建待用加密，包括索引、同義字對應，以及索引子、資料來源和技能集的定義。 Azure 認知搜尋也支援新增客戶管理的金鑰 (CMK) 來補充加密索引內容。 針對在 1 2020 年8月之後建立的服務，CMK 加密可延伸至暫存磁片上的資料，以完整雙重加密索引內容。

+ 輸入安全性可保護搜尋服務端點在提高安全性層級：從要求上的 API 金鑰，到防火牆中的輸入規則，到從公用網際網路全面防護您的服務的私人端點。

+ 輸出安全性適用于從外部來源提取內容的索引子。 針對輸出要求，請設定受控識別，以在存取來自 Azure 儲存體、Azure SQL、Cosmos DB 或其他 Azure 資料來源的資料時，讓搜尋成為受信任的服務。 受控識別會取代連接上的認證或存取金鑰。 本文未涵蓋輸出安全性。 如需這項功能的詳細資訊，請參閱 [使用受控識別連接到資料來源](search-howto-managed-identities-data-sources.md)。

觀賞這段快速的影片，瞭解安全性架構及每個功能類別的總覽。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>加密的傳輸和儲存體

在 Azure 認知搜尋中，加密會以連線和傳輸為開頭，並延伸至儲存在磁片上的內容。 若為公用網際網路上的搜尋服務，Azure 認知搜尋會接聽 HTTPS 埠443。 所有用戶端對服務連線都會使用 TLS 1.2 加密。 不支援舊版 (1.0 或 1.1) 。

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="描述每個服務參與層級之不同安全性類型的圖表":::

針對搜尋服務在內部處理的資料，下表描述 [資料加密模型](../security/fundamentals/encryption-models.md)。 某些功能（例如知識存放區、增量擴充和索引子型索引、讀取或寫入其他 Azure 服務中的資料結構）。 這些服務本身具有不同于 Azure 認知搜尋的加密支援層級。

| 型號 | 鑰匙&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 要求&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 限制 | 適用於 |
|------------------|-------|-------------|--------------|------------|
| 伺服器端加密 | Microsoft 管理的金鑰 | 無 (內建)  | 無，適用于所有區域中的所有層級（在 24 2018 年1月之後建立的內容）。 | 內容 (索引和同義字對應) 和定義 (索引子、資料來源、技能集)  |
| 伺服器端加密 | 客戶管理的金鑰 | Azure 金鑰保存庫 | 適用于所有區域中的可計費層級（適用于2019年1月之後建立的內容）。 | 資料磁片上) 的內容 (索引和同義字對應 |
| 伺服器端雙重加密 | 客戶管理的金鑰 | Azure 金鑰保存庫 | 適用于所選區域的可計費層級，于 1 2020 年8月之後的搜尋服務上。 | 資料磁片和暫存磁片上的內容 (索引和同義字對應)  |

### <a name="service-managed-keys"></a>服務管理的金鑰

服務管理的加密是一種 Microsoft 內部作業，以使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)的[Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)為基礎。 它會自動出現在所有索引上，包括未完全加密 (在) 2018 年1月之前建立之索引的累加式更新。

### <a name="customer-managed-keys-cmk"></a>客戶管理的金鑰 (CMK) 

客戶管理的金鑰需要額外的可計費服務，Azure Key Vault 可以位於不同的區域，但在與 Azure 認知搜尋相同的訂用帳戶下。 啟用 CMK 加密將會增加索引大小並降低查詢效能。 根據至今的觀察值，您可以預期在查詢時間中會有 30%-60% 的增加，不過實際的效能將視索引定義和查詢類型而有所不同。 由於這項效能的影響，我們建議您只在真正需要它的索引上啟用這項功能。 如需詳細資訊，請參閱 [Azure 認知搜尋中的設定客戶管理的加密金鑰](search-security-manage-encryption-keys.md)。

<a name="double-encryption"></a>

### <a name="double-encryption"></a>雙重加密

在 Azure 認知搜尋中，雙重加密是 CMK 的延伸模組。 它會被視為兩個 (一次的加密，也就是由服務管理的金鑰) 和廣泛的範圍中，涵蓋寫入至資料磁片的長期儲存空間，以及寫入暫存磁片的短期儲存體。 CMK 在 1 2020 年8月之前與之後的差異，以及在 Azure 認知搜尋中 CMK 雙重加密功能的方式，是在暫存磁片上進行待用資料的額外加密。

在8月1日之後于這些區域中建立的新服務上，目前提供雙重加密：

+ 美國西部 2
+ 美國東部
+ 美國中南部
+ US Gov 維吉尼亞州
+ US Gov 亞利桑那州

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>輸入安全性和 endpoint protection

輸入安全性功能會透過增加的安全性和複雜性層級來保護搜尋服務端點。 首先，所有要求都需要 API 金鑰以進行驗證存取。 其次，您可以選擇性地設定防火牆規則，以限制對特定 IP 位址的存取。 若為 advanced protection，第三個選項是讓 Azure Private Link 從所有網際網路流量防護您的服務端點。

### <a name="public-access-using-api-keys"></a>使用 API 金鑰的公用存取

根據預設，搜尋服務會透過公用雲端存取，並使用對搜尋服務端點的系統管理員或查詢存取的金鑰型驗證。 [API 金鑰](search-security-rbac.md)是由隨機產生的數位和字母所組成的字串。 金鑰的類型 (管理或查詢) 會決定存取層級。 提交有效的金鑰可證明要求源自受信任的實體。

您的搜尋服務有兩個存取層級，由下列 API 金鑰啟用：

+ 管理金鑰 (允許對搜尋服務進行「 [建立-讀取-更新-刪除](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 」作業的讀寫存取) 

+ 查詢金鑰 (允許唯讀存取索引的檔集合) 

布建服務時，會建立系統 *管理金鑰*。 有兩個管理員金鑰，指定為主要和次要以將它們保持在各自的位置，但事實上，它們是可互換。 每個服務有兩個管理員金鑰，以便您在轉換其中一個時不會無法存取您的服務。 您可以根據 Azure 安全性最佳作法定期 [重新產生管理金鑰](search-security-api-keys.md#regenerate-admin-keys) ，但無法新增至總管理金鑰計數。 每個搜尋服務最多可有兩個系統管理金鑰。

*查詢金鑰* 是視需要建立的，而且是針對發出查詢的用戶端應用程式所設計。 您最多可以建立 50 個查詢金鑰。 在應用程式程式碼中，您可以指定搜尋 URL 和查詢 api 金鑰，以允許唯讀存取特定索引的檔集合。 端點、可供唯讀存取的 API 金鑰以及目標索引共同定義來自用戶端應用程式連接的範圍和存取層級。

針對每個要求都必須進行驗證，其中每個要求會由強制性金鑰、作業及物件所組成。 當兩個權限層級 (完整和唯讀) 加上內容 (例如，索引上的查詢作業) 鏈結在一起時，即足以在服務作業上提供全面的安全性。 如需關於金鑰的詳細資訊，請參閱[建立及管理 API 金鑰](search-security-api-keys.md)。

### <a name="ip-restricted-access"></a>IP 限制的存取

若要進一步控制搜尋服務的存取權，您可以建立允許存取特定 IP 位址或 IP 位址範圍的輸入防火牆規則。 所有用戶端連線都必須透過允許的 IP 位址進行，否則連接會遭到拒絕。

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="ip 限制存取的範例架構圖表":::

您可以使用入口網站來 [設定輸入存取權](service-configure-firewall.md)。

或者，您也可以使用管理 REST Api。 從 API 版本2020-03-13 開始，使用 [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) 參數，您可以識別您想要授與存取權給搜尋服務的 IP 位址，以限制對服務的存取。

### <a name="private-endpoint-no-internet-traffic"></a>私人端點 (沒有網際網路流量) 

Azure 認知搜尋的 [私人端點](../private-link/private-endpoint-overview.md) 可讓 [虛擬網路](../virtual-network/virtual-networks-overview.md) 上的用戶端透過 [Private Link](../private-link/private-link-overview.md)，安全地存取搜尋索引中的資料。

私人端點會使用來自虛擬網路位址空間的 IP 位址來連接到您的搜尋服務。 用戶端與搜尋服務之間的網路流量會在 Microsoft 骨幹網路上進行虛擬網路和私人連結，以消除公用網際網路的暴露。 VNET 允許資源之間的安全通訊，以及內部部署網路和網際網路。

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="私人端點存取的範例架構圖表":::

雖然這是最安全的解決方案，但使用額外的服務會增加成本，因此請務必清楚瞭解這些優點，再進行深入瞭解。 如需成本的詳細資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/private-link/)。 如需這些元件如何搭配運作的詳細資訊，請觀看本文頂端的影片。 私人端點選項的涵蓋範圍從5:48 開始進入影片。 如需有關如何設定端點的指示，請參閱 [建立 Azure 認知搜尋的私人端點](service-create-private-endpoint.md)。

## <a name="index-access"></a>索引存取權

在 Azure 認知搜尋中，個別索引不是安全物件。 相反地，索引的存取權是在服務層級決定， (服務) 的讀取或寫入存取權，以及作業的內容。

針對使用者存取權，您可以使用查詢金鑰來建立查詢要求的結構，此 [查詢金鑰](search-security-rbac.md)會將任何要求設為唯讀，並且包含您的應用程式所使用的特定索引。 在查詢要求中，並沒有聯結索引或同時存取多個索引的概念，所以所有要求都會依據定義以單一索引為目標。 因此，查詢要求本身的結構 (一個金鑰加上單一目標索引) 即可定義安全性界限。

系統管理員和開發人員對索引的存取權並無差別：兩者都需要有寫入存取權，才能建立、刪除及更新服務所管理的物件。 具有服務 [管理金鑰](search-security-rbac.md) 的任何人都可以讀取、修改或刪除相同服務中的任何索引。 在防止發生意外或惡意刪除索引的情況方面，您內部對程式碼資產實施的來源控制，將會是反轉不需要之索引刪除或修改的補救措施。 Azure 認知搜尋在叢集中進行容錯移轉以確保可用性，但不會儲存或執行您用來建立或載入索引的專屬程式碼。

以需要索引層級之安全性界限的多組織用戶管理解決方案來說，這類解決方案通常會包含可供客戶用來處理索引隔離的中間層。 如需多租使用者使用案例的詳細資訊，請參閱多租使用者 [SaaS 應用程式和 Azure 認知搜尋的設計模式](search-modeling-multitenant-saas-applications.md)。

## <a name="user-access"></a>使用者存取

使用者存取索引和其他物件的方式取決於要求上的 API 金鑰類型。 大部分的開發人員會為用戶端搜尋要求建立並指派[查詢金鑰](search-security-api-keys.md)。 查詢金鑰會將唯讀存取權授與索引中可搜尋的內容。

如果您需要對搜尋結果進行細微的每個使用者控制，您可以在查詢上建立安全性篩選，並傳回與指定的安全性識別相關聯的檔。 身分識別型存取控制會實作為 *篩選* 條件，以根據身分識別來修剪檔和內容的搜尋結果，而不是預先定義的角色和角色指派。 下表說明兩個針對未經授權的內容縮減搜尋結果的方法。

| 方法 | 說明 |
|----------|-------------|
|[根據身分識別篩選進行安全性範圍縮減](search-security-trimming-for-azure-search.md)  | 記載實作使用者身分識別存取控制的基本工作流程。 其中涵蓋在索引中新增安全性識別碼，然後說明如何針對該欄位進行篩選來縮減所禁止內容的結果。 |
|[根據 Azure Active Directory 身分識別進行安全性範圍縮減](search-security-trimming-for-azure-search-with-aad.md)  | 本文將擴充先前的文章，提供從 Azure 雲端平臺中的其中一項 [免費服務](https://azure.microsoft.com/free/) Azure Active Directory (Azure AD) 中取得身分識別的步驟。 |

## <a name="administrative-rights"></a>系統管理許可權

[Azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md) 是以布建 azure 資源的 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 為基礎的授權系統。 在 Azure 認知搜尋中，Resource Manager 可用來建立或刪除服務、管理 API 金鑰，以及調整服務規模。 因此，Azure 角色指派會決定誰可以執行這些工作，不論他們使用的是 [入口網站](search-manage.md)、 [POWERSHELL](search-manage-powershell.md)或 [管理 REST api](/rest/api/searchmanagement/search-howto-management-rest-api)。

相反地，在服務上裝載之內容的系統管理員許可權（例如建立或刪除索引的能力）是透過 API 金鑰授予，如上 [一節](#index-access)中所述。

> [!TIP]
> 使用全 Azure 的機制，您可以鎖定訂用帳戶或資源，以防止具有系統管理員許可權的使用者遭到意外或未經授權刪除搜尋服務。 如需詳細資訊，請參閱 [鎖定資源以防止非預期的刪除](../azure-resource-manager/management/lock-resources.md)。

## <a name="certifications-and-compliance"></a>認證和合規性

Azure 認知搜尋已通過認證，適用于公用雲端和 Azure Government 的多重全球、區域及業界特定標準。 如需完整清單，請從官方 Audit reports 頁面下載 [ **Microsoft Azure 合規性供應** 專案白皮書](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)。

為了符合規範，您可以使用 [Azure 原則](../governance/policy/overview.md) 來實行 [Azure 安全性基準測試](../security/benchmarks/introduction.md)的高安全性最佳作法。 Azure 安全性效能評定是一組安全性建議的集合，編寫至安全性控制項，以對應至緩解服務和資料的威脅時應採取的重要動作。 目前有11個安全性控制項，包括 [網路安全性](../security/benchmarks/security-control-network-security.md)、 [記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)，以及 [資料保護](../security/benchmarks/security-control-data-protection.md) 等等。

Azure 原則是 Azure 內建的功能，可協助您管理多個標準的合規性，包括 Azure 安全性基準測試。 針對已知的基準測試，Azure 原則提供內建定義，可提供這兩個準則以及可採取動作的回應，以解決不符合規範的狀況。

針對 Azure 認知搜尋，目前有一個內建的定義。 它是用於診斷記錄。 使用此內建，您可以指派原則來識別缺少診斷記錄的任何搜尋服務，然後將其開啟。 如需詳細資訊，請參閱 [Azure 原則 Azure 認知搜尋的法規合規性控制](security-controls-policy.md)。

## <a name="see-also"></a>請參閱

+ [Azure 安全性基本概念](../security/fundamentals/index.yml)
+ [Azure 安全性](https://azure.microsoft.com/overview/security)
+ [Azure 資訊安全中心](../security-center/index.yml)
