---
title: 何謂 Azure Key Vault？ | Microsoft Docs
description: 瞭解 Azure 金鑰保存庫如何保護雲應用程式和服務使用的加密金鑰和機密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 03ad504258dd9448753f37402067a0da3e0a2c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197635"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure 金鑰保存庫基本概念

Azure Key Vault 是可安全儲存及存取祕密的工具。 祕密是指任何需受到嚴密存取控制的項目，例如 API 金鑰、密碼或憑證。 保存庫是一組邏輯機密。

以下是其他重要術語：

- **租用戶**：租用戶是擁有並且可管理特定 Microsoft 雲端服務執行個體的組織。 它最常用於指組織的 Azure 和 Office 365 服務集。

- **保存庫擁有者**：保存庫擁有者可建立金鑰保存庫，並取得其完整存取權和控制權。 保存庫擁有者也可以設定稽核，以記錄存取祕密和金鑰的人員。 系統管理員可控制金鑰的生命週期。 他們可將金鑰變換為新版本、備份金鑰，以及執行相關工作。

- **保存庫取用者**：保存庫取用者可在保存庫擁有者為取用者授與存取權時，對金鑰保存庫內的資產執行動作。 可用的動作取決於授與的權限。

- **資源**：資源是可透過 Azure 提供的可管理項目。 常見示例包括虛擬機器、存儲帳戶、Web 應用、資料庫和虛擬網路。 還有更多。

- **資源群組**：資源群組是可保存 Azure 解決方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。

- **服務主體**：Azure 服務主體是使用者創建的應用、服務和自動化工具用於訪問特定 Azure 資源的安全標識。 將其視為具有特定角色的"使用者身份"（使用者名和密碼或證書），以及嚴格控制的許可權。 不同於一般的使用者身分識別，服務主體只需要執行特定動作。 如果僅授予它執行其管理工作所需的最低權限等級，則它可提高安全性。

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)：Azure AD 是租用戶的 Active Directory 服務。 每個目錄都有一或多個網域。 一個目錄可以有多個相關聯的訂用帳戶，但只能有一個租用戶。

- **Azure 租用戶識別碼**：租用戶識別碼是對 Azure 訂用帳戶內的 Azure AD 執行個體進行識別的唯一方法。

- **託管標識**：Azure 金鑰保存庫提供了一種安全存儲憑據和其他金鑰和機密的方法，但代碼需要對金鑰保存庫進行身份驗證才能檢索它們。 使用受控識別可以輕易地解決此問題，因為它可在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別向 Key Vault 或任何支援 Azure AD 驗證的服務進行驗證，而無須在程式碼中使用任何認證。 有關詳細資訊，請參閱以下映射和[Azure 資源的託管標識概述](../active-directory/managed-identities-azure-resources/overview.md)。

    ![Azure 資源的託管標識工作圖](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>驗證
要對金鑰保存庫執行任何操作，首先需要對其進行身份驗證。 有三種方法可以對金鑰保存庫進行身份驗證：

- [Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)：在 Azure 中的虛擬機器上部署應用時，可以將標識分配給有權訪問金鑰保存庫的虛擬機器。 還可以將標識分配給[其他 Azure 資源](../active-directory/managed-identities-azure-resources/overview.md)。 此方法的好處是應用或服務沒有管理第一個秘密的輪換。 Azure 會自動輪換身分識別。 我們建議將此方法作為最佳實踐。 
- **服務主體和證書**：您可以使用服務主體和有權訪問金鑰保存庫的關聯證書。 我們不建議使用此方法，因為應用程式擁有者或開發人員必須輪換證書。
- **服務主體和機密**：雖然您可以使用服務主體和機密對金鑰保存庫進行身份驗證，但我們不推薦它。 很難自動旋轉用於驗證金鑰保存庫的引導金鑰。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表將有助於瞭解金鑰保存庫可以如何協助達到開發人員和安全性系統管理員的需求。

| 角色 | 問題陳述 | Azure 金鑰保存庫已解決問題 |
| --- | --- | --- |
| Azure 應用程式的開發人員 |"我想為 Azure 編寫一個應用程式，該應用程式使用金鑰進行簽名和加密。 但是，我希望這些金鑰從我的應用程式外部，以便解決方案適合地理分佈的應用程式。 <br/><br/>我希望這些金鑰和密碼會受到保護，但我可以不用自己撰寫程式碼。 我也希望從我的應用程式就可以輕鬆使用這些金鑰和密碼，並得到最佳效能。」 |√ 金鑰會儲存在保存庫中，並且視需要由 URI 叫用。<br/><br/> √ Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組保護金鑰。<br/><br/> √ 金鑰會在與應用程式位於相同 Azure 資料中心的 HSM 中處理。 此方法會比金鑰位於不同位置 (例如內部部署) 提供更高的可靠性並減少延遲。 |
| 軟體即服務 (SaaS) 的開發人員 |「對於客戶之租用戶的金鑰和密碼，我不想承擔任何實際或潛在法律責任。 <br/><br/>我希望客戶擁有並自行管理金鑰，這樣我就可以將全部精力集中在我的專長上，也就是提供核心軟體功能。」 |√ 客戶可以將他們自己的金鑰匯入 Azure 並加以管理。 當 SaaS 應用程式需要使用客戶的金鑰執行加密操作時，金鑰保存庫代表應用程式執行這些操作。 應用程式不會看到客戶的金鑰。 |
| 資訊安全長 (CSO) |「我想要知道我們的應用程式是否遵守 FIPS 140-2 Level 2 HSM 的安全金鑰管理規定。 <br/><br/>我想要確定我的組織妥善掌控金鑰生命週期，並可監視金鑰使用情形。 <br/><br/>而且即使我們使用多個 Azure 服務和資源，我仍想要從 Azure 中的單一位置管理金鑰。」 |√ HSM 已通過 FIPS 140-2 Level 2 驗證。<br/><br/>√ 金鑰保存庫經過設計，所以 Microsoft 不會看見或擷取您的金鑰。<br/><br/>√ 金鑰使用情形會近乎即時地進行記錄。<br/><br/>√ 不論您在 Azure 中有幾個保存庫、保存庫支援哪些區域，以及哪些應用程式使用這些保存庫，保存庫均提供單一介面讓您清楚了解。 |

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 儘管 Key Vault 使開發人員和安全管理員受益，但可以通過管理其他 Azure 服務的組織的管理員實現和管理它。 例如，此管理員可以使用 Azure 訂閱登錄，為要存儲金鑰的組織創建一個保存庫，然後負責以下操作任務：

- 建立或匯入金鑰或密碼
- 撤銷或刪除金鑰或密碼
- 授權使用者或應用程式存取金鑰保存庫，以便他們管理或使用其金鑰和密碼
- 設定金鑰使用方法 (例如，簽署或加密)
- 監視金鑰使用方法

然後，此管理員向開發人員提供 URI，以便從其應用程式調用。 此管理員還會向安全管理員提供金鑰使用方式日誌記錄資訊。 

![Azure 金鑰保存庫運作方式的概觀][1]

開發人員也可透過使用 API 直接管理金鑰。 有關詳細資訊，請參閱[金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>後續步驟

瞭解如何[保護您的保存庫](key-vault-secure-your-key-vault.md)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。
