---
title: Azure Key Vault 安全性概觀
description: 概要說明 Azure Key Vault 的安全性功能和最佳作法。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c4e9f0cd6a7421f5be29200816bb00a56b141367
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937595"
---
# <a name="azure-key-vault-security-overview"></a>Azure Key Vault 安全性概觀

您可以使用 Azure Key Vault 來保護雲端中的加密金鑰和秘密，例如憑證、連接字串和密碼。 當您儲存機密和商務關鍵資料時，您需要採取步驟，將保存庫的安全性和儲存在其中的資料最大化。

本文概述 Azure Key Vault 的安全性功能和最佳作法。 如需根據 [Azure 安全性基準測試](../../security/benchmarks/introduction.md)的完整建議清單，請參閱 Azure Key Vault 的 [安全性基準](security-baseline.md)。

## <a name="general-guidance"></a>一般方針

我們的建議是在每個環境中使用每個應用程式的保存庫 (開發、進入生產階段前和生產) 。 這可協助您在不同的環境中共用秘密，也可在發生缺口時減少威脅。

## <a name="network-security"></a>網路安全性

您可以指定存取保存庫所使用的 IP 位址來減少保存庫的暴露。 Azure Key Vault 的虛擬網路服務端點可讓您將存取權限制為指定的虛擬網路。 這些端點也可讓您將存取權限制為 IPv4 (網際網路通訊協定第 4 版) 位址範圍的清單。 任何從這些來源之外連線到金鑰保存庫的使用者都會被拒絕存取。  如需完整的詳細資訊，請參閱 [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)

防火牆規則生效後，使用者只能在其要求源自允許的虛擬網路或 IPv4 位址範圍時，才可以讀取 Key Vault 中的資料。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。  如需執行步驟，請參閱 [設定 Azure Key Vault 防火牆和虛擬網路](network-security.md)

Azure Private Link 服務可讓您透過虛擬網路中的私人端點存取 Azure Key Vault 和 Azure 裝載的客戶/合作夥伴服務。 Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。  如需執行步驟，請參閱 [整合 Key Vault 與 Azure Private Link](private-link-service.md)

## <a name="tls-and-https"></a>TLS 和 HTTPS

- Key Vault 前端 (資料平面) 是多租使用者伺服器。 這表示來自不同客戶的金鑰保存庫可以共用相同的公用 IP 位址。 為了達成隔離，每個 HTTP 要求都會與其他要求分開進行驗證和授權。
- 您可以識別舊版的 TLS 來回報弱點，但因為公用 IP 位址是共用的，所以 key vault 服務小組無法針對傳輸層級的個別金鑰保存庫停用舊版 TLS。
- HTTPS 通訊協定可讓用戶端參與 TLS 協商。 **用戶端可以強制執行最新版本的 TLS**，而且每當用戶端執行此動作時，整個連接都會使用對應的層級保護。 Key Vault 仍然支援舊版 TLS 的事實不會影響使用較新 TLS 版本的連線安全性。
- 儘管 TLS 通訊協定有已知的弱點，但在攻擊者使用具有弱點的 TLS 版本連線時，並沒有任何已知的攻擊可讓惡意代理程式從您的金鑰保存庫中解壓縮任何資訊。 攻擊者仍然需要自行驗證及授權，而且只要合法的用戶端一律與最新的 TLS 版本連線，就無法在舊版的 TLS 版本上洩漏認證。

## <a name="identity-management"></a>身分識別管理

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 嘗試從保存庫管理或擷取內容的任何人都必須由 Azure AD 進行驗證。 在這兩種情況下，應用程式都可以透過三種方式存取 Key Vault：

- **僅限應用程式**：應用程式代表服務主體或受控識別。 對於定期需要從金鑰保存庫存取憑證、金鑰或密碼的應用程式，此身分識別是最常見的案例。 若要讓此案例正常運作， `objectId` 必須在存取原則中指定應用程式的，且 `applicationId` 不得指定或必須是 `null` 。
- **僅限使用者**：使用者從租使用者中註冊的任何應用程式存取金鑰保存庫。 舉例來說，這類存取包括 Azure PowerShell 和 Azure 入口網站。 若要讓此案例正常運作， `objectId` 必須在存取原則中指定使用者的，且 `applicationId` 不得指定或必須是 `null` 。
- **應用程式 plus-使用者** (有時稱為 _複合身分識別_) ：使用者必須從特定應用程式存取金鑰保存庫 _，而且_ 應用程式必須使用代理者驗證 (OBO) 流程來模擬使用者。 若要讓此案例正常運作 `applicationId` ， `objectId` 必須在存取原則中指定和。 會 `applicationId` 識別所需的應用程式，並 `objectId` 識別使用者。 目前，此選項不適用於資料平面 Azure RBAC (預覽版) 。

在所有類型的存取中，應用程式會使用 Azure AD 進行驗證。 應用程式會根據應用程式類型使用任何[支援的驗證方法](../../active-directory/develop/authentication-vs-authorization.md)。 應用程式會取得平面中資源的權杖以便授與存取權。 視 Azure 環境而定，資源會是管理或資料平面中的端點。 應用程式會使用此權杖，對 Key Vault 傳送 REST API 要求。 若要深入了解，請參閱[整個驗證流程](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

如需完整的詳細資訊，請參閱 [Key Vault Authentication 基礎](authentication-fundamentals.md)

## <a name="privileged-access"></a>特殊權限存取

授權則會判斷呼叫者可以執行的作業。 Key Vault 中的授權會使用 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 和 Azure Key Vault 存取原則的組合。

保存庫的存取會透過兩個介面或平面進行。 這些平面包括管理平面和資料平面。

- *管理平面* 是您管理 Key Vault 本身的位置，而且這是用來建立和刪除保存庫的介面。 您也可以讀取 Key Vault 屬性並管理存取原則。
- *資料平面* 可讓您處理金鑰保存庫中儲存的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

應用程式會透過端點來存取平面。 這兩個平面的存取控制各自獨立運作。 若要授與應用程式存取權以使用金鑰保存庫中的金鑰，您可以使用 Key Vault 存取原則或 Azure RBAC (preview) 來授與資料平面存取權。 若要授與使用者對 Key Vault 屬性和標記的讀取權限，但無法存取 (金鑰、秘密或憑證) 的資料，您可以使用 Azure RBAC 來授與管理平面存取權。

下表顯示管理和資料平面的端點。

| 存取&nbsp;平面 | 存取端點 | 作業 | 存取&nbsp;控制機制 |
| --- | --- | --- | --- |
| 管理平面 | **全域：**<br> management.azure.com:443<br><br> **Azure China 21Vianet：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 | 建立、讀取、更新及刪除金鑰保存庫<br><br>設定 Key Vault 存取原則<br><br>設定 Key Vault 標籤 | Azure RBAC |
| 資料平面 | **全域：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Gov︰**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 金鑰：加密、解密、wrapKey、unwrapKey、簽署、驗證、取得、列出、建立、更新、匯入、刪除、復原、備份、還原、清除<br><br> 憑證： managecontacts、getissuers、listissuers、setissuers、deleteissuers、manageissuers、get、list、建立、匯入、更新、刪除、復原、備份、還原、清除<br><br>  秘密：取得、列出、設定、刪除、復原、備份、還原、清除 | Key Vault 存取原則或 Azure RBAC (預覽) |

### <a name="managing-administrative-access-to-key-vault"></a>管理 Key Vault 的系統管理存取權

當您在資源群組中建立金鑰保存庫時，可以使用 Azure AD 管理存取權。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以指派適當的 Azure 角色，以授與特定範圍層級的存取權。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 下列範圍層級可以指派給 Azure 角色：

- **訂** 用帳戶：在訂用帳戶層級指派的 Azure 角色會套用到該訂用帳戶內的所有資源群組和資源。
- **資源群組**：在資源群組層級指派的 Azure 角色會套用至該資源群組中的所有資源。
- **特定資源**：指派給特定資源的 Azure 角色會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱 [AZURE RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。

### <a name="controlling-access-to-key-vault-data"></a>控制對 Key Vault 資料的存取

Key Vault 存取原則可分別授與金鑰、祕密或憑證的權限。 您只能對使用者授與存取金鑰 (而非祕密) 的權限。 金鑰、祕密和憑證的存取權限是在保存庫層級管理的。

> [!IMPORTANT]
> Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。

您可以設定金鑰保存庫的存取原則使用 [Azure 入口網站](assign-access-policy-portal.md)、 [Azure CLI](assign-access-policy-cli.md)、 [Azure PowerShell](assign-access-policy-powershell.md)或 [Key Vault 管理 REST api](/rest/api/keyvault/)。

您可以使用 [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)來限制資料平面的存取)。 您可以設定[防火牆和虛擬網路規則](network-security.md)來額外加上一層安全性。

## <a name="logging-and-monitoring"></a>記錄和監視

Key Vault 記錄會儲存在保存庫上執行之活動的相關資訊。 如需完整的詳細資訊，請參閱 [Key Vault 記錄](logging.md)。

您可以將 Key Vault 與事件方格整合，以便在金鑰保存庫中儲存的金鑰、憑證或密碼狀態變更時收到通知。 如需詳細資訊，請參閱 [使用 Azure 事件方格監視 Key Vault](event-grid-overview.md)

也請務必監視金鑰保存庫的健康情況，以確保您的服務會如預期般運作。 若要瞭解如何進行，請參閱 [Azure Key Vault 的監視和警示](alert.md)。

## <a name="backup-and-recovery"></a>備份與復原

Azure Key Vault 虛刪除和清除保護可讓您復原已刪除的保存庫和保存庫物件。 如需完整的詳細資訊，請參閱 [Azure Key Vault 虛刪除總覽](soft-delete-overview.md)。

您也應該在保存庫中的物件更新/刪除/建立時，定期備份保存庫。  

Azure PowerShell 備份命令：

* [備份憑證](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [備份金鑰](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [備份密碼](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

Azure CLI 備份命令

* [備份憑證](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [備份金鑰](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [備份密碼](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)
- [Azure RBAC：內建角色](../../role-based-access-control/built-in-roles.md)
