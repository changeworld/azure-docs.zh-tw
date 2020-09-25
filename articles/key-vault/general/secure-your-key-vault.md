---
title: 針對金鑰保存庫的存取進行保護
description: Azure Key Vault 的存取模型，包括 Active Directory 驗證和資源端點。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 9516a32e89b9ad671cf705c8f520c73e28801c19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320586"
---
# <a name="secure-access-to-a-key-vault"></a>針對金鑰保存庫的存取進行保護

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 本文提供 Key Vault 存取模型的概觀。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

## <a name="access-model-overview"></a>存取模型概觀

金鑰保存庫的存取權可透過兩個介面來控制︰管理平面和資料平面。 管理平面可讓您管理 Key Vault 本身。 此平面中的作業包括建立和刪除金鑰保存庫、擷取 Key Vault 屬性，以及更新存取原則。 資料平面則可讓您處理金鑰保存庫中儲存的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

若要在任一平面存取金鑰保存庫，所有呼叫者 (使用者或應用程式) 都必須有適當的驗證和授權。 驗證會建立呼叫者的身分識別。 授權則會判斷呼叫者可以執行哪些作業。

兩個平面都會使用 Azure Active Directory (Azure AD) 來進行驗證。 針對授權，管理平面會使用 Azure 角色型存取控制 (RBAC) 而資料平面會使用 Key Vault 存取原則和 Azure RBAC (預覽) 。

## <a name="active-directory-authentication"></a>Active Directory 驗證

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 這兩個平面中的所有呼叫者都必須在此租用戶中註冊，並經過驗證才能存取金鑰保存庫。 在這兩種情況中，應用程式均可透過兩種方式存取 Key Vault︰

- **僅限應用程式**：應用程式代表服務或背景工作。 對於定期需要從金鑰保存庫存取憑證、金鑰或密碼的應用程式，此身分識別是最常見的案例。 若要讓此案例正常運作， `objectId` 必須在存取原則中指定應用程式的，且 `applicationId` 不得_not_指定或必須是 `null` 。
- **僅限使用者**：使用者從租使用者中註冊的任何應用程式存取金鑰保存庫。 舉例來說，這類存取包括 Azure PowerShell 和 Azure 入口網站。 若要讓此案例正常運作， `objectId` 必須在存取原則中指定使用者的，且 `applicationId` 不得指定_not_或必須是 `null` 。
- **應用程式 plus-使用者** (有時稱為 _複合身分識別_) ：使用者必須從特定應用程式存取金鑰保存庫 _，而且_ 應用程式必須使用代理者驗證 (OBO) 流程來模擬使用者。 若要讓此案例正常運作 `applicationId` ， `objectId` 必須在存取原則中指定和。 會 `applicationId` 識別所需的應用程式，並 `objectId` 識別使用者。 目前，此選項不適用於資料平面 Azure RBAC (預覽版) 。

在所有類型的存取中，應用程式會使用 Azure AD 進行驗證。 應用程式會根據應用程式類型使用任何[支援的驗證方法](../../active-directory/develop/authentication-scenarios.md)。 應用程式會取得平面中資源的權杖以便授與存取權。 視 Azure 環境而定，資源會是管理或資料平面中的端點。 應用程式會使用此權杖，對 Key Vault 傳送 REST API 要求。 若要深入了解，請參閱[整個驗證流程](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

這兩個平面的單一驗證機制模型有幾個優點：

- 組織可以集中控制其組織內所有金鑰保存庫的存取權。
- 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
- 組織可以藉由使用 Azure AD 中的選項 (例如，啟用多重要素驗證以提升安全性) 來自訂驗證。

## <a name="resource-endpoints"></a>資源端點

應用程式會透過端點來存取平面。 這兩個平面的存取控制各自獨立運作。 若要授與應用程式存取權以使用金鑰保存庫中的金鑰，您可以使用 Key Vault 存取原則或 Azure RBAC (preview) 來授與資料平面存取權。 若要對使用者授與讀取 Key Vault 屬性和標籤的存取權，但不授與讀取資料 (金鑰、密碼或憑證) 的存取權，您可以使用 RBAC 來對管理平面授與存取權。

下表顯示管理和資料平面的端點。

| 存取&nbsp;平面 | 存取端點 | 作業 | 存取&nbsp;控制機制 |
| --- | --- | --- | --- |
| 管理平面 | **全域：**<br> management.azure.com:443<br><br> **Azure China 21Vianet：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 | 建立、讀取、更新及刪除金鑰保存庫<br><br>設定 Key Vault 存取原則<br><br>設定 Key Vault 標籤 | Azure RBAC |
| 資料平面 | **全域：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Gov︰**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 金鑰：加密、解密、wrapKey、unwrapKey、簽署、驗證、取得、列出、建立、更新、匯入、刪除、復原、備份、還原、清除<br><br> 憑證： managecontacts、getissuers、listissuers、setissuers、deleteissuers、manageissuers、get、list、建立、匯入、更新、刪除、復原、備份、還原、清除<br><br>  秘密：取得、列出、設定、刪除、復原、備份、還原、清除 | Key Vault 存取原則或 Azure RBAC (預覽) |

## <a name="management-plane-and-azure-rbac"></a>管理平面和 Azure RBAC

在管理平面中，您會使用 Azure 角色型存取控制 (Azure RBAC) 來授權呼叫者可以執行的作業。 在 Azure RBAC 模型中，每個 Azure 訂用帳戶都有 Azure AD 的實例。 您可以對來自該目錄的使用者、群組和應用程式授與存取權。 授與存取權即可在 Azure 訂用帳戶中管理使用 Azure Resource Manager 部署模型的資源。

您可以使用 Azure AD 在資源群組中建立金鑰保存庫和管理存取權。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以藉由指派適當的 Azure 角色，授與特定範圍層級的存取權。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 下列範圍層級可以指派給 Azure 角色：

- **訂**用帳戶：在訂用帳戶層級指派的 Azure 角色會套用到該訂用帳戶內的所有資源群組和資源。
- **資源群組**：在資源群組層級指派的 Azure 角色會套用至該資源群組中的所有資源。
- **特定資源**：指派給特定資源的 Azure 角色會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>資料平面和存取原則

您可以藉由設定金鑰保存庫 Key Vault 存取原則，來授與資料平面存取權。 若要設定這些存取原則，使用者、群組或應用程式必須具有該金鑰保存庫管理平面的 `Contributor` 權限。

您可以對使用者、群組或應用程式授與權限，來為金鑰保存庫中的金鑰或密碼執行特定作業。 Key Vault 最多可支援 1,024 個存取原則項目。 若要對數名使用者授與資料平面存取權，請建立 Azure AD 安全性群組並在該群組中新增使用者。

您可以在這裡看到保存庫和秘密作業的完整清單： [Key Vault 操作參考](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a>Key Vault 存取原則可分別授與金鑰、祕密和憑證的權限。  金鑰、祕密和憑證的存取權限位於保存庫層級。 

> [!IMPORTANT]
> Key Vault 存取原則會套用在保存庫層級。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。
Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 
>

## <a name="data-plane-and-azure-rbac-preview"></a>資料平面和 Azure RBAC (預覽版) 

Azure 角色型存取控制是一個替代的許可權模型，用來控制可在個別金鑰保存庫上啟用 Azure Key Vault 資料平面的存取權。 Azure RBAC 許可權模型是專屬的，一旦設定，保存庫存取原則就會變成非使用中。 Azure Key Vault 會定義一組 Azure 內建角色，其中包含用來存取金鑰、秘密或憑證的一般許可權集。

當 Azure 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權可以設定為訂用帳戶層級、資源群組、金鑰保存庫，或個別金鑰、秘密或憑證的範圍。 Azure AD 的安全性主體可能是使用者、群組、應用程式服務主體，或 [適用于 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。

對保存庫存取原則使用 Azure RBAC 許可權的主要優點是集中式存取控制管理，並與 Privileged Identity Management (PIM) 整合。 Privileged Identity Management 提供以時間為基礎和以核准為基礎的角色啟用，可降低因重要資源上有過多、不必要或誤用的存取權限而帶來的風險。


## <a name="firewalls-and-virtual-networks"></a>防火牆與虛擬網路

針對額外的安全性層級，您可以設定防火牆和虛擬網路規則。 根據預設，您可以將 Key Vault 防火牆和虛擬網路設定成拒絕存取所有網路流量 (包括網際網路流量)。 您可以授權存取特定 Azure 虛擬網路和公用網際網路 IP 位址範圍的流量，讓您為應用程式建置安全的網路界限。

以下是如何使用服務端點的一些範例：

* 您會使用 Key Vault 來儲存加密金鑰、應用程式祕密和憑證，而且您想要禁止從公用網際網路存取您的金鑰保存庫。
* 您想要鎖定您金鑰保存庫的存取權，以致只有您的應用程式或少數幾個指定的主機可以連線到您的金鑰保存庫。
* 您有在 Azure 虛擬網路中執行的應用程式，而此虛擬網路已針對所有的輸入和輸出流量進行鎖定。 您的應用程式仍然需要連線至 Key Vault，以擷取祕密或憑證，或使用密碼編譯金鑰。

> [!NOTE]
> Key Vault 防火牆和虛擬網路規則只適用於 Key Vault 的資料平面。 Key Vault 控制平面作業 (例如建立、刪除和修改作業、設定存取原則、設定防火牆和虛擬網路規則) 不受防火牆和虛擬網路規則影響。

## <a name="private-endpoint-connection"></a>私人端點連接

如果需要完全封鎖公開 Key Vault 的風險，則可以使用 Azure 私人端點。 Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

針對 Azure 服務使用 Private Link 的常見案例：

- **私下存取 Azure 平台上的服務**：將您的虛擬網路連線至 Azure 中的服務，而不需要來源或目的地的公用 IP 位址。 服務提供者可以在自己的虛擬網路中呈現其服務，而取用者可以在其本機虛擬網路中存取這些服務。 Private Link 平台會透過 Azure 骨幹網路處理取用者與服務之間的連線。 
 
- **內部部署及對等互連的網路**：使用私人端點透過 ExpressRoute 私人對等互連、VPN 通道及對等互連虛擬網路，從內部部署裝置存取在 Azure 中執行的服務。 無須設定公用對等互連或經由網際網路來存取服務。 Private Link 可安全地將工作負載遷移至 Azure。
 
- **防止資料外洩**：私人端點會對應到 PaaS 資源的執行個體，而不是整個服務。 取用者只能連線至特定資源。 服務中任何其他資源的存取都會遭到封鎖。 此機制可防範資料外洩風險。 
 
- **觸及全球**：私下連線至其他區域中執行的服務。 取用者的虛擬網路可能在區域 A 中，但可以連線至區域 B 中 Private Link 後方的服務。  
 
- **延伸至您自己的服務**：啟用相同的體驗和功能，將您的服務私下呈現給 Azure 中的取用者。 藉由將您的服務放在標準 Azure Load Balancer 後方，您就可以將其用於 Private Link。 然後，取用者就可以使用本身虛擬網路中的私人端點，直接連線至您的服務。 您可以使用核准呼叫流程來管理連線要求。 Azure Private Link 可用於屬於不同 Azure Active Directory 租用戶的取用者和服務。 

## <a name="example"></a>範例

在此範例中，我們要開發一個應用程式，以使用 TLS/SSL 的憑證、Azure 儲存體儲存資料，以及使用 RSA 2048 位金鑰來加密 Azure 儲存體中的資料。 我們的應用程式會在 Azure 虛擬機器 (VM) (或虛擬機器擴展集) 中執行。 我們可以使用金鑰保存庫來儲存應用程式祕密。 我們可以儲存供應用程式用來向 Azure AD 進行驗證的啟動程序憑證。

我們需要存取下列預存金鑰和祕密：
- **TLS/SSL 憑證**：用於 TLS/SSL。
- **儲存體金鑰**：用來存取儲存體帳戶。
- **RSA 2048 位金鑰**： Azure 儲存體用來包裝/解除包裝資料加密金鑰。
- **應用程式受控識別**：用來向 Azure AD 進行驗證。 授與 Key Vault 的存取權之後，應用程式就可以提取儲存體金鑰和憑證。

我們必須定義下列角色，以指定誰可以管理、部署及稽核我們的應用程式：
- **安全性小組**：CSO (首席安全官) 辦公室的 IT 人員或類似的參與者。 安全性小組負責妥善保管祕密。 秘密可以包含 TLS/SSL 憑證、用於加密的 RSA 金鑰、連接字串和儲存體帳戶金鑰。
- **開發人員和操作員**：這些人負責開發應用程式，並將其部署在 Azure 中。 此小組的成員不屬於安全性工作人員。 這些人不應具備敏感性資料 (例如 TLS/SSL 憑證與 RSA 金鑰) 的存取權。 其所部署的應用程式才應具備敏感性資料的存取權。
- **稽核員**：此角色適用於不是開發小組成員或一般 IT 人員的參與者。 其會檢閱憑證、金鑰和祕密的使用與維護情形，以確保各項作業符合安全性標準。

另外還有一個不在我們應用程式範圍內的角色：訂用帳戶 (或資源群組) 系統管理員。 訂用帳戶管理員會設定安全性小組的初始存取權限。 其會使用資源群組 (具有此應用程式所需的資源)，將存取權授與安全性小組。

我們需要對我們的角色授權下列作業：

**安全性小組**
- 建立金鑰保存庫。
- 開啟 Key Vault 記錄。
- 新增金鑰和祕密。
- 建立金鑰備份以用於災害復原。
- 設定 Key Vault 存取原則和指派角色，以將許可權授與使用者和應用程式進行特定的作業。
- 定期輪替金鑰和祕密。

**開發人員和操作員**
- 取得安全性小組的參考，以取得啟動程式和 TLS/SSL 憑證 (指紋) 、儲存體金鑰 (秘密 URI) ，以及用於包裝/解除包裝的 RSA 金鑰 (金鑰 URI) 。
- 開發和部署應用程式，以程式設計方式存取憑證和秘密。

**稽核員**
- 檢閱 Key Vault 記錄，確認金鑰和密碼的使用是否適當，以及是否符合資料安全性標準。

下表摘要說明角色和應用程式的存取權限。

| 角色 | 管理平面權限 | 資料平面許可權-保存庫存取原則 | 資料平面許可權-Azure RBAC (預覽版)   |
| --- | --- | --- | --- |
| 安全性小組 | Key Vault 參與者 | 憑證：所有作業 <br> 金鑰：所有作業 <br> 祕密：所有作業 | Key Vault 系統管理員 (預覽)  |
| 開發人員和&nbsp;操作員 | Key Vault 部署權限<br><br> **注意**：此權限可讓已部署的 VM 從金鑰保存庫擷取祕密。 | None | None |
| 稽核員 | None | 憑證：清單 <br> 金鑰︰列出<br>密碼︰列出<br><br> **注意**：此權限可讓稽核員檢查未在記錄中顯現的金鑰和密碼所具有的屬性 (標籤、啟用日和到期日)。 | Key Vault 讀者 (預覽)  |
| Azure 儲存體帳戶 | 無 | 索引鍵： get、list、wrapKey、unwrapKey <br> | Key Vault 加密服務加密 |
| Application | None | 秘密： get、list <br> 憑證： get、list | Key Vault 讀者 (預覽) 、Key Vault Secret 使用者 (preview)  |

這三個小組角色需要其他資源的存取權以及 Key Vault 權限。 若要部署 Vm (或 Azure App Service) 的 Web Apps 功能，開發人員和操作員需要部署存取權。 稽核員需要儲存 Key Vault 記錄所在儲存體帳戶的讀取權限。

我們的範例會說明一個簡單的案例。 真實案例可能會更複雜。 您可以根據需求調整金鑰保存庫的權限。 我們假設該安全性小組會提供 DevOps 人員在其應用程式中所使用的金鑰和密碼參考 (URI 和指紋)。 開發人員和操作員不需要任何資料平面存取權。 我們的重點放在如何保護您的金鑰保存庫。

> [!NOTE]
> 此範例示範生產環境中會如何鎖定 Key Vault 的存取權。 開發人員應該有自己的訂用帳戶或資源群組，並擁有完整權限以便管理其用來開發應用程式的保存庫、VM 和儲存體帳戶。

## <a name="resources"></a>資源

* [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>後續步驟

[向 Azure Key Vault 進行驗證](authentication.md)

[指派 Key Vault 存取原則](assign-access-policy-portal.md)

[將 Azure 角色指派給金鑰、秘密和憑證的存取權](rbac-guide.md)

[設定 Key Vault 防火牆和虛擬網路](network-security.md)

[建立與 Key Vault 的私人連結連接](private-link-service.md)
