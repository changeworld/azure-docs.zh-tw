---
title: 資料庫安全性 - Azure Cosmos DB
description: 了解 Azure Cosmos DB 如何為您的資料提供資料庫保護和資料安全性。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 35bfc45ffffac17a2eca40cb51c2db5d94c140a2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487461"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 的安全性 - 概觀

本文討論 Azure Cosmos DB 提供的資料庫安全性最佳作法和重要功能，協助您防止、偵測及因應資料庫缺口。

## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB 安全性的新功能

儲存在所有 Azure 區域之 Azure Cosmos DB 中的文件和備份現在可以使用待用加密。 這些區域內的新舊客戶都會自動套用加密靜止功能。 無須設定任何項目；您即可獲得跟之前相同的出色延遲、輸送量、可用性及功能，並擁有以下好處：知道資料在加密靜止下仍舊安全無虞。

## <a name="how-do-i-secure-my-database"></a>如何? 保護我的資料庫

資料安全性是您、客戶和資料庫提供者之間共同的責任。 根據您選擇的資料庫提供者而定，您承擔的責任也有所不同。 如果您選擇內部部署解決方案，您需要負責從端點保護到硬體實體安全性的一切事項，這並不容易。 如果您選擇 PaaS 雲端資料庫提供者，例如 Azure Cosmos DB，您關切的領域會大幅縮小。 下圖取自 Microsoft 的[雲端運算共同責任 (英文)](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) 技術白皮書，顯示您的責任如何藉助於 PaaS 提供者 (例如 Azure Cosmos DB) 而減輕。

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="客戶和資料庫提供者的責任":::

上圖顯示高階雲端安全性元件，但對於您的資料庫解決方案，您需要特別關注哪些項目？ 還有，您如何比較不同的解決方案？

我們建議採用下列需求檢查清單來比較資料庫系統︰

- 網路安全性和防火牆設定
- 使用者驗證和細微的使用者控制
- 能夠將資料複寫到世界各地以防區域性失敗
- 從一個資料中心容錯移轉至另一個資料中心的能力
- 在資料中心內的區域資料複寫
- 自動資料備份
- 從備份中還原已刪除的資料
- 保護並隔離機密資料
- 監視攻擊
- 回應攻擊
- 能夠異地隔離資料以遵守資料控管限制
- 實際保護受保護資料中心內的伺服器
- 認證

雖然看似明顯，但最新的[大規模資料庫缺口](https://thehackernews.com/2017/01/mongodb-database-security.html)提醒我們注意以下這些簡單但很重要的需求︰

- 修補保持最新狀態的伺服器
- 預設的 HTTPS/TLS 加密
- 使用強式密碼的系統管理帳戶

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Azure Cosmos DB 如何保護我的資料庫

讓我們回顧先前的清單 - Azure Cosmos DB 提供其中多少安全性需求？ 每一個。

讓我們詳細探究每一個。

|安全性需求|Azure Cosmos DB 的安全性方法|
|---|---|
|網路安全性|使用 IP 防火牆是保護資料庫的第一道防線。 Azure Cosmos DB 支援原則驅動的 IP 型存取控制，以提供輸入防火牆支援。 IP 型存取控制類似于傳統資料庫系統所使用的防火牆規則，但會加以擴充，以便只能從一組核准的機器或雲端服務存取 Azure Cosmos 資料庫帳戶。 若要深入瞭解，請參閱 [Azure Cosmos DB 防火牆支援](how-to-configure-firewall.md) 文章。<br><br>Azure Cosmos DB 可讓您啟用特定的 IP 位址 (168.61.48.0)、IP 範圍 (168.61.48.0/8)，以及 IP 和範圍的組合。 <br><br>Azure Cosmos DB 會封鎖此允許清單之外的機器發出的所有要求。 因此，核准的機器和雲端服務發出的要求必須完成驗證程序，才能獲得資源的存取控制。<br><br> 您可以使用 [虛擬網路服務](../virtual-network/service-tags-overview.md) 標籤來達成網路隔離，並保護您的 Azure Cosmos DB 資源免于受到一般網際網路的限制。 建立安全性規則時，請以服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如，AzureCosmosDB) 在規則的適當來源或目的地欄位中，您可以允許或拒絕對應服務的流量。|
|授權|Azure Cosmos DB 使用雜湊式訊息驗證碼 (HMAC) 來進行授權。 <br><br>每個要求都經過祕密帳戶金鑰的雜湊處理，而後續的 base 64 編碼雜湊會隨著每次呼叫 Azure Cosmos DB 一起傳送。 為了驗證要求，Azure Cosmos DB 服務會使用正確的祕密金鑰和屬性產生雜湊，然後比較此值與要求中的值。 如果兩個值相符，則會成功授權作業，並處理要求，否則授權會失敗，也會拒絕要求。<br><br>您可以使用 [主要金鑰](#primary-keys)或 [資源權杖](secure-access-to-data.md#resource-tokens) ，讓您可以更細緻地存取資源（例如檔）。<br><br>請參閱[保護 Azure Cosmos DB 資源的存取](secure-access-to-data.md)以深入了解。|
|使用者和權限|使用帳戶的主要金鑰時，您可以為每個資料庫建立使用者資源和許可權資源。 資源權杖與資料庫中的權限相關聯，將決定使用者是否能夠存取 (讀寫、唯讀或無法存取) 資料庫中的應用程式資源。 應用程式資源包括容器、文件、附件、預存程序、觸發程序和 UDF。 然後，驗證期間會使用資源權杖來允許或拒絕存取資源。<br><br>請參閱[保護 Azure Cosmos DB 資源的存取](secure-access-to-data.md)以深入了解。|
|Azure 目錄整合 (RBAC)| 您也可以使用 Azure 入口網站中的存取控制 (IAM) ，來提供或限制 Cosmos 帳戶、資料庫、容器和供應專案 (輸送量) 的存取權。 IAM 提供角色型存取控制，並與 Active Directory 整合。 您可以針對個人和群組使用內建角色或自訂角色。 如需詳細資訊，請參閱 [Active Directory 整合](role-based-access-control.md) 文章。|
|全球複寫|Azure Cosmos DB 提供現成的全域散發，您只要按一下按鈕，就能將資料複寫至 Azure 的任何一個全球資料中心。 全球複寫可讓您擴大到全球規模，確保存取世界各地資料時的低延遲性。<br><br>在安全性方面，全域複寫可確保資料不受區域性失敗影響。<br><br>請參閱[將資料分散到全球](distribute-data-globally.md)以深入了解。|
|區域性容錯移轉|如果您已將資料複寫至多個資料中心，萬一某個區域資料中心離線，Azure Cosmos DB 會自動轉移您的作業。 您可以使用已複寫資料的區域，建立容錯移轉區域的優先順序清單。 <br><br>請參閱 [Azure Cosmos DB 的區域性容錯移轉](high-availability.md)以深入了解。|
|區域複寫|即使在單一資料中心內，Azure Cosmos DB 也會自動複寫資料來達到高可用性，還可讓您選擇[一致性層級](consistency-levels.md)。 這種複寫保證所有單一區域帳戶和所有多重區域帳戶的 99.99% [可用性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) ，在所有多區域資料庫帳戶上都有寬鬆的一致性，以及99.999% 的讀取可用性。|
|自動化線上備份|Azure Cosmos 資料庫會定期備份，並儲存在異地冗余存放區中。 <br><br>請參閱[使用 Azure Cosmos DB 進行自動線上備份及還原](online-backup-and-restore.md)以深入了解。|
|還原已刪除的資料|自動化線上備份可用來復原您不小心刪除的資料，最多可還原意外發生後 30 天內的資料。 <br><br>[使用 Azure Cosmos DB 深入瞭解自動線上備份和還原](online-backup-and-restore.md)|
|保護並隔離機密資料|新功能中所列的區域中的所有資料均已加密靜止。<br><br>個人資料和其他機密資料可隔離至特定容器來進行讀寫，或限制為只對特定使用者提供唯讀存取權。|
|監視攻擊|您可以使用[稽核記錄和活動記錄](./monitor-cosmos-db.md)，以監視帳戶的正常和異常活動。 您可以檢視資源上執行的作業、誰起始作業、作業何時發生、作業的狀態等等，如本表格之後的螢幕擷取畫面所示。|
|回應攻擊|一旦您連絡 Azure 支援來報告潛在的攻擊，就會展開 5 步驟的事件回應程序。 5 步驟程序的目標是在偵測到問題並展開調查之後，儘快恢復正常的服務安全性和作業。<br><br>請參閱[雲端的 Microsoft Azure 安全性回應](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)以深入了解。|
|異地隔離|Azure Cosmos DB 可確保主權區域 (例如，德國、中國、US Gov) 的資料控管。|
|受保護的設施|Azure Cosmos DB 中的資料儲存在 Azure 受保護資料中心內的 SSD 上。<br><br>請參閱 [Microsoft 全球資料中心](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)以深入了解|
|HTTPS/SSL/TLS 加密|Azure Cosmos DB 的所有連接都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2。<br>您可以強制執行最低的 TLS 版本伺服器端。 若要這樣做，請聯絡 [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) 。|
|待用加密|所有儲存至 Azure Cosmos DB 的資料都會進行待用加密。 若要深入了解，請參閱 [Azure Cosmos DB 待用加密](./database-encryption-at-rest.md)|
|修補的伺服器|Azure Cosmos DB 是受控資料庫，會自動替您管理和修補伺服器。|
|使用強式密碼的系統管理帳戶|很難相信我們需要提到這個需求，但是與一些競爭者不同，Azure Cosmos DB 中的系統管理帳戶一定會有密碼。<br><br> 根據預設，透過 TLS 和 HMAC 密碼型驗證的安全性是內建。|
|安全性和資料保護認證| 如需最新的認證清單，請參閱整體 [Azure 合規性網站](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) ，以及具備所有認證的最新 [Azure 合規性檔](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (搜尋 Cosmos) 。 如需更詳細的資訊，請閱讀2018年4月25日的文章 [Azure #CosmosDB：安全、私密、符合規範，包括 SOCS 1/2 Type 2、HITRUST、PCI DSS Level 1、ISO 27001、HIPAA、FedRAMP High 和其他許多專案。

以下螢幕擷取畫面顯示如何使用稽核記錄和活動記錄來監視您的帳戶：:::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="客戶和資料庫提供者的責任":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>主索引鍵

主鍵提供資料庫帳戶所有系統管理資源的存取權。 主鍵：

- 允許存取帳戶、資料庫、使用者和權限。 
- 無法用來提供容器和文件的更細微的存取權。
- 在帳戶建立期間建立。
- 可隨時重新產生。

每個帳戶都是由兩個主要金鑰組成：主鍵和次要金鑰。 雙重金鑰的目的是讓您可以重新產生或輸替金鑰，以持續存取您的帳戶和資料。

除了 Cosmos DB 帳戶的兩個主要金鑰，還有兩個唯讀金鑰。 這些唯讀金鑰只允許帳戶上的讀取作業。 唯讀金鑰不提供存取權來讀取權限資源。

您可以使用 Azure 入口網站來抓取和重新產生主要、次要、唯讀和讀寫的主要金鑰。 相關指示請參閱[檢視、複製和重新產生存取金鑰](manage-with-cli.md#regenerate-account-key)。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="客戶和資料庫提供者的責任":::

## <a name="next-steps"></a>後續步驟

如需主要金鑰和資源權杖的詳細資訊，請參閱 [保護 Azure Cosmos DB 資料的存取](secure-access-to-data.md)。

如需有關審核記錄的詳細資訊，請參閱 [Azure Cosmos DB 診斷記錄](./monitor-cosmos-db.md)。

如需 Microsoft 認證的詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。