---
title: 何謂 Azure Key Vault？ | Microsoft Docs
description: 瞭解 Azure Key Vault 如何保護雲端應用程式和服務所使用的密碼編譯金鑰和秘密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: e0bb3c3f3a6a1a38f974acf361937928ad4e2cfd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983290"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault 基本概念

Azure Key Vault 是可安全儲存及存取秘密的雲端服務。 秘密是您想要嚴密控制存取權的任何一項，例如 API 金鑰、密碼、憑證或密碼編譯金鑰。 Key Vault 服務支援兩種類型的容器：保存庫和受管理的 HSM 集區。 保存庫支援儲存軟體和 HSM 支援的金鑰、秘密和憑證。 受管理的 HSM 集區僅支援 HSM 支援的金鑰。 如需完整的詳細資訊，請參閱 [Azure Key Vault REST API 總覽](about-keys-secrets-certificates.md) 。

以下是其他重要詞彙：

- **租用戶**：租用戶是擁有並且可管理特定 Microsoft 雲端服務執行個體的組織。 它最常用來參考組織的 Azure 和 Office 365 服務集合。

- **保存庫擁有者**：保存庫擁有者可建立金鑰保存庫，並取得其完整存取權和控制權。 保存庫擁有者也可以設定稽核，以記錄存取祕密和金鑰的人員。 系統管理員可控制金鑰的生命週期。 他們可將金鑰變換為新版本、備份金鑰，以及執行相關工作。

- **保存庫取用者**：保存庫取用者可在保存庫擁有者為取用者授與存取權時，對金鑰保存庫內的資產執行動作。 可用的動作取決於授與的權限。

- **受管理的 Hsm 系統管理員**：獲派系統管理員角色的使用者可完全控制受管理的 hsm 集區。 他們可以建立更多角色指派，以將控制的存取權委派給其他使用者。

- **受控 Hsm 加密長/使用者**：通常會指派給使用者或服務主體的內建角色，這些角色會使用受控 HSM 中的金鑰執行密碼編譯作業。 加密使用者可以建立新的金鑰，但無法刪除金鑰。

- **受控 HSM 加密服務加密**：通常會指派給服務帳戶受控服務身分識別的內建角色 (例如儲存體帳戶) 使用客戶管理的金鑰來加密待用資料。

- **資源**：資源是可透過 Azure 提供的可管理項目。 常見的範例包括虛擬機器、儲存體帳戶、web 應用程式、資料庫和虛擬網路。 還有更多。

- **資源群組**：資源群組是可保存 Azure 解決方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。

- **安全性主體**： Azure 安全性主體是使用者建立的應用程式、服務和自動化工具用來存取特定 Azure 資源的安全性身分識別。 將它視為「使用者身分識別」 (使用者名稱和密碼，或具有特定角色的憑證) ，以及嚴格控制的許可權。 不同于一般使用者身分識別，安全性主體應該只需要進行特定動作。 如果您只授與執行其管理工作所需的最低許可權等級，則可提升安全性。 與應用程式或服務搭配使用的安全性主體，特別稱為「 **服務主體**」。

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md)：Azure AD 是租用戶的 Active Directory 服務。 每個目錄都有一或多個網域。 一個目錄可以有多個相關聯的訂用帳戶，但只能有一個租用戶。

- **Azure 租用戶識別碼**：租用戶識別碼是對 Azure 訂用帳戶內的 Azure AD 執行個體進行識別的唯一方法。

- **受控**識別： Azure Key Vault 提供安全地儲存認證和其他金鑰和秘密的方式，但您的程式碼必須向 Key Vault 進行驗證才能取得它們。 使用受控識別可以輕易地解決此問題，因為它可在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別向 Key Vault 或任何支援 Azure AD 驗證的服務進行驗證，而無須在程式碼中使用任何認證。 如需詳細資訊，請參閱下圖和 [Azure 資源受控](../../active-directory/managed-identities-azure-resources/overview.md)識別的總覽。

    ![適用于 Azure 資源的受控識別如何運作的圖表](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>驗證
若要使用 Key Vault 進行任何作業，您必須先對其進行驗證。 有三種方式可向 Key Vault 進行驗證：

- [適用于 azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別：當您在 azure 中的虛擬機器上部署應用程式時，您可以將身分識別指派給可存取 Key Vault 的虛擬機器。 您也可以將身分識別指派給 [其他 Azure 資源](../../active-directory/managed-identities-azure-resources/overview.md)。 這種方法的優點是應用程式或服務不會管理第一個秘密的輪替。 Azure 會自動輪換身分識別。 我們建議採用這種方法做為最佳作法。 
- **服務主體和憑證**：您可以使用可存取 Key Vault 的服務主體和相關聯的憑證。 我們不建議採用此方法，因為應用程式擁有者或開發人員必須輪替憑證。
- **服務主體和秘密**：雖然您可以使用服務主體和秘密來驗證 Key Vault，但我們並不建議這麼做。 很難自動將用來驗證 Key Vault 的啟動程式密碼旋轉。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表將有助於瞭解金鑰保存庫可以如何協助達到開發人員和安全性系統管理員的需求。

| 角色 | 問題陳述 | Azure 金鑰保存庫已解決問題 |
| --- | --- | --- |
| Azure 應用程式的開發人員 |「我想要撰寫使用金鑰進行簽署和加密的 Azure 應用程式。 但我希望這些金鑰從應用程式外部，以便讓解決方案適用于地理位置分散的應用程式。 <br/><br/>我希望這些金鑰和密碼會受到保護，但我可以不用自己撰寫程式碼。 我還希望這些金鑰和秘密可讓我輕鬆地從應用程式使用，並獲得最佳效能。」 |√ 金鑰會儲存在保存庫中，並且視需要由 URI 叫用。<br/><br/> √ Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組保護金鑰。<br/><br/> √ 金鑰會在與應用程式位於相同 Azure 資料中心的 HSM 中處理。 此方法會比金鑰位於不同位置 (例如內部部署) 提供更高的可靠性並減少延遲。 |
| 軟體即服務 (SaaS) 的開發人員 |「我不希望客戶的租使用者金鑰和秘密有責任或潛在責任。 <br/><br/>我希望客戶擁有並管理他們的金鑰，讓我能夠專注于如何執行最棒的動作，也就是提供核心軟體功能。」 |√ 客戶可以將他們自己的金鑰匯入 Azure 並加以管理。 當 SaaS 應用程式需要使用客戶的金鑰來執行密碼編譯作業時，Key Vault 會代表應用程式執行這些作業。 應用程式不會看到客戶的金鑰。 |
| 資訊安全長 (CSO) |「我想要知道我們的應用程式符合 FIPS 140-2 層級2或 FIPS 140-2 Level 3 Hsm 的安全金鑰管理。 <br/><br/>我想要確定我的組織妥善掌控金鑰生命週期，並可監視金鑰使用情形。 <br/><br/>雖然我們使用多個 Azure 服務和資源，但是我想要從 Azure 中的單一位置管理金鑰。」 |√選擇 FIPS 140-2 Level 2 驗證的 Hsm 的保存 **庫** 。<br/>√為 FIPS 140-2 層級3驗證的 Hsm 選擇 **受管理的 hsm** 集區。<br/><br/>√ 金鑰保存庫經過設計，所以 Microsoft 不會看見或擷取您的金鑰。<br/>√ 金鑰使用情形會近乎即時地進行記錄。<br/><br/>√ 不論您在 Azure 中有幾個保存庫、保存庫支援哪些區域，以及哪些應用程式使用這些保存庫，保存庫均提供單一介面讓您清楚了解。 |

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 雖然 Key Vault 可受益于開發人員和安全性系統管理員，但它可以由管理其他 Azure 服務的組織系統管理員來執行和管理。 例如，此系統管理員可以使用 Azure 訂用帳戶登入、為組織建立用來儲存金鑰的保存庫，然後負責執行如下的作業工作：

- 建立或匯入金鑰或密碼
- 撤銷或刪除金鑰或密碼
- 授權使用者或應用程式存取金鑰保存庫，以便他們管理或使用其金鑰和密碼
- 設定金鑰使用方法 (例如，簽署或加密)
- 監視金鑰使用方法

然後，這個系統管理員會提供開發人員 Uri，以從其應用程式呼叫。 此系統管理員也會將金鑰使用方式記錄資訊提供給安全性系統管理員。 

![Azure 金鑰保存庫運作方式的概觀][1]

開發人員也可透過使用 API 直接管理金鑰。 如需詳細資訊，請參閱 [《 Key Vault 開發人員指南》](developers-guide.md)。

## <a name="next-steps"></a>下一步

- 瞭解如何 [保護您](secure-your-key-vault.md)的保存庫。
- 瞭解如何[保護受管理的 HSM](../managed-hsm/access-control.md)集區

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。
