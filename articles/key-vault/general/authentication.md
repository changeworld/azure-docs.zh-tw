---
title: 向 Azure Key Vault 進行驗證
description: 了解如何向 Azure Key Vault 進行驗證
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 455caf0b80d82b03f8d00929addeab15a1af6d7e
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754210"
---
# <a name="authenticate-to-azure-key-vault"></a>向 Azure Key Vault 進行驗證

Azure Key Vault 可讓您在集中式、安全的雲端存放庫中儲存祕密並控管其散發，而不需要將認證儲存在應用程式中。 應用程式只需要在執行階段使用 Key Vault 進行驗證，即可存取這些祕密。

## <a name="app-identity-and-security-principals"></a>應用程式身分識別與安全性主體

使用 Key Vault 進行驗證時，會與 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 搭配使用，其會負責驗證任何指定 **安全性主體** 的身分識別。

安全性主體是一個物件，代表要求存取 Azure 資源的使用者、群組、服務或應用程式。 Azure 會為每個安全性主體指派唯一的 **物件識別碼**。

* **使用者** 安全性主體會識別在 Azure Active Directory 中具有設定檔的個人。

* **群組** 安全性主體會識別在 Azure Active Directory 中建立的一組使用者。 指派給群組的任何角色或權限都會授與群組中的所有使用者。

* **服務主體** 是一種安全性主體，會識別應用程式或服務 (也就是一段程式碼，而不是使用者或群組)。 服務主體的物件識別碼稱為其 **用戶端識別碼**，作用就像其使用者名稱。 服務主體的 **用戶端密碼** 作用就像其密碼。

有兩種方式可取得應用程式服務主體：

* 建議您為應用程式啟用系統指派的 **受控識別**。

    使用受控識別，Azure 會在內部管理應用程式的服務主體，並使用其他 Azure 服務自動驗證應用程式。 部署至各種服務的應用程式均可使用受控識別。

    如需詳細資訊，請參閱[受控識別概觀](../../active-directory/managed-identities-azure-resources/overview.md)。 另請參閱[支援受控識別的 Azure 服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)連結，包括描述如何為特定服務 (例如 App Service、Azure Functions、虛擬機器等等) 啟用受控識別的文章。

* 如果無法使用受控識別，請改為向 Azure AD 租用戶 **註冊** 應用程式，如 [快速入門：向 Microsoft 身分識別平台註冊應用程式](../../active-directory/develop/quickstart-register-app.md)中所述。 註冊也會建立可在所有租用戶中識別應用程式的第二個應用程式物件。

## <a name="authorize-a-security-principal-to-access-key-vault"></a>授權安全性主體存取 Key Vault

Key Vault 適用於兩種不同的授權層級：

- **存取原則** 控制是否授權使用者、群組或服務主體存取現有 Key Vault 資源「內」的祕密、金鑰和憑證 (有時也稱為「資料平面」作業)。 存取原則通常會授與使用者、群組和應用程式。

    若要指派存取原則，請參閱下列文章：

    - [Azure 入口網站](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **角色權限** 會控制是否授權使用者、群組或服務主體建立、刪除及管理 Key Vault 資源 (也稱為「管理平面」作業)。 通常只會將這類角色授與管理員。
 
    若要指派和管理角色，請參閱下列文章：

    - [Azure 入口網站](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Key Vault 目前支援[參與者](../../role-based-access-control/built-in-roles.md#key-vault-contributor)角色，允許在 Key Vault 資源上進行管理作業。 有一些其他角色目前為預覽狀態。 您也可以建立自訂角色，如 [Azure 自訂角色](../../role-based-access-control/custom-roles.md)所述。

    如需角色的一般資訊，請參閱[什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。


> [!IMPORTANT]
> 為了達到最佳安全性，請一律遵循最低權裉的主體，並僅授與所需的最特定存取原則和角色。 
    
## <a name="configure-the-key-vault-firewall"></a>設定 Key Vault 防火牆

根據預設，Key Vault 允許透過公用 IP 位址來存取資源。 為了達到最佳安全性，也可以限制只能存取特定 IP 範圍、服務端點、虛擬網路或私人端點。

如需詳細資訊，請參閱[存取防火牆後方的 Azure Key Vault](./access-behind-firewall.md)。


## <a name="the-key-vault-authentication-flow"></a>Key Vault 驗證流程

1. 服務主體會要求向 Azure AD 進行驗證，例如：
    * 使用者可以藉由使用者名稱和密碼登入 Azure 入口網站。
    * 應用程式會叫用 Azure REST API，並出示用戶端識別碼和祕密或用戶端憑證。
    * Azure 資源 (例如具有受控識別的虛擬機器) 會連線到 [Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) REST 端點，以取得存取權杖。

1. 如果成功向 AAD 驗證，則會將 OAuth 權杖授與服務主體。

1. 服務主體會透過 Key Vault 的端點 (URI) 呼叫 Key Vault REST API。

1. Key Vault 防火牆會檢查下列準則。 如果符合任一條件，則允許呼叫。 否則會封鎖呼叫，並傳回禁止的回應。

    * 防火牆已停用，且可從公用網際網路連線 Key Vault 的公用端點。
    * 呼叫者是 [Key Vault 信任的服務](./overview-vnet-service-endpoints.md#trusted-services)，可讓其略過防火牆。
    * 呼叫者會依 IP 位址、虛擬網路或服務端點列示在防火牆中。
    * 呼叫者可以透過設定的私人連結連線觸達 Key Vault。    

1. 如果防火牆允許呼叫，Key Vault 會呼叫 Azure AD 來驗證服務主體的存取權杖。

1. Key Vault 會檢查服務主體是否具有所要求之作業的必要存取原則。 如果沒有，Key Vault 會傳回禁止的回應。

1. Key Vault 會執行要求的作業並傳回結果。

下圖說明應用程式呼叫 Key Vault「取得秘密」API 的程序：

![Azure Key Vault 驗證流程](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault 適用於祕密、憑證和金鑰的 SDK 用戶端會對沒有存取權杖的 Key Vault 進行額外的呼叫，這會導致401 回應以擷取租用戶資訊。 如需詳細資訊，請參閱[驗證、要求與回應](authentication-requests-and-responses.md)

## <a name="code-examples"></a>程式碼範例

下表會連結到不同文章，示範如何使用 Azure SDK 程式庫來處理應用程式程式碼中的 Key Vault，以取得有問題的語言。 為了方便起見，會納入例如 Azure CLI 和 Azure 入口網站等其他介面。

| Key Vault 祕密 | Key Vault 金鑰 | Key Vault 憑證 |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET (SDK v4)](../secrets/quick-create-net.md) | -- | -- |
| [.NET (SDK v3)](https://dotnet.microsoft.com/download/dotnet-core/3.0) | -- | -- |
| [Java](../secrets/quick-create-java.md) | -- | -- |
| [JavaScript](../secrets/quick-create-node.md) | -- | -- | 
| | | |
| [Azure 入口網站](../secrets/quick-create-portal.md) | [Azure 入口網站](../keys/quick-create-portal.md) | [Azure 入口網站](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM 範本](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>後續步驟

- [Key Vault 存取原則疑難排解](troubleshooting-access-issues.md)
- [Key Vault REST API 錯誤碼](rest-error-codes.md)
- [金鑰保存庫開發人員指南](developers-guide.md)
- [什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
