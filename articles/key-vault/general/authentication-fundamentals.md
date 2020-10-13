---
title: Azure Key Vault authentication 基礎
description: 深入瞭解 key vault 的驗證模型的運作方式
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: c8d2304017a8fccd83c9c64559b8c5edf48481b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604270"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 驗證基本概念

Azure Key Vault 可讓您安全地儲存和管理應用程式認證，例如秘密、金鑰和憑證，在中央和安全的雲端存放庫中。 Key Vault 無須將認證儲存在您的應用程式中。 您的應用程式可以在執行時間進行驗證 Key Vault，以取得認證。

以系統管理員身分，您可以嚴格地控制哪些使用者和應用程式可以存取您的金鑰保存庫，而且您可以限制和審核它們所執行的作業。 本檔說明金鑰保存庫存取模型的基本概念。 它會提供您入門層級的知識，並向您示範如何從開始到結束，向金鑰保存庫驗證使用者或應用程式。

## <a name="required-knowledge"></a>必要知識

本檔假設您已熟悉下列概念。 如果您不熟悉任何這些概念，請在繼續之前先遵循說明連結。

* Azure Active Directory [連結](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
* 安全性主體 [連結](https://docs.microsoft.com/azure/key-vault/general/authentication#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault 設定步驟摘要

1. 在 Azure Active Directory 中註冊您的使用者或應用程式做為安全性主體。
1. 在 Azure Active Directory 中設定安全性主體的角色指派。
1. 為您的安全性主體設定 key vault 存取原則。
1. 設定 Key Vault 防火牆存取您的金鑰保存庫 (選擇性) 。
1. 測試您的安全性主體存取金鑰保存庫的能力。

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>在 Azure Active Directory 中將使用者或應用程式註冊為安全性主體

當使用者或應用程式對金鑰保存庫提出要求時，要求必須先由 Azure Active Directory 進行驗證。 若要這麼做，使用者或應用程式必須在 Azure Active Directory 中註冊為安全性主體。

請依照下列檔連結，瞭解如何在 Azure Active Directory 中註冊使用者或應用程式。
**請確定您為應用程式建立使用者註冊的密碼，以及用戶端密碼或用戶端憑證認證。**

* 在 Azure Active Directory[連結](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)中註冊使用者
* 在 Azure Active Directory[連結](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)中註冊應用程式

## <a name="assign-your-security-principal-a-role-in-azure-active-directory"></a>在 Azure Active Directory 中將角色指派給安全性主體

Azure Active Directory 使用角色型存取控制 (RBAC) 將許可權指派給安全性主體。 這些許可權稱為角色指派。

在金鑰保存庫的內容中，這些角色指派會決定安全性主體的管理平面存取層級 (也稱為金鑰保存庫的控制平面) 。 這些角色指派不會直接提供資料平面秘密的存取權，但會提供管理金鑰保存庫屬性的存取權。 例如，被指派「 **讀者」角色** 的使用者或應用程式將無法變更金鑰保存庫的防火牆設定，而指派「 **參與者」角色** 的使用者或應用程式可以進行變更。 這兩個角色都不會有直接存取權來執行秘密、金鑰和憑證的作業，例如建立或抓取其值，直到指派金鑰保存庫資料平面的存取權為止。 在下一個步驟中會說明這一點。

>[!IMPORTANT]
> 雖然具有「參與者」或「擁有者」角色的使用者沒有存取權，無法對儲存在金鑰保存庫中的秘密執行作業，但「參與者」和「擁有者」角色會提供許可權，以新增或移除金鑰保存庫中所儲存之秘密的存取原則。 因此，具有這些角色指派的使用者可以授與自己存取金鑰保存庫中秘密的存取權。 基於這個理由，建議只有系統管理員能夠存取參與者或擁有者角色。 只需要從金鑰保存庫取得秘密的使用者和應用程式，應該會被授與讀取者角色。 **下一節中的詳細資料。**

>[!NOTE]
> 當您將角色指派指派給 Azure Active Directory 租使用者層級的使用者時，這組許可權會向下 trickle 到指派範圍內的所有訂用帳戶、資源群組和資源。 若要遵循最低許可權的主體，您可以在更細微的範圍中建立此角色指派。 例如，您可以為使用者指派訂用帳戶層級的讀取者角色，以及單一金鑰保存庫的擁有者角色。 移至訂用帳戶、資源群組或金鑰保存庫的身分識別存取管理 (IAM) 設定，以在更細微的範圍中建立角色指派。

* 若要深入瞭解 Azure Active Directory 角色 [連結](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
* 深入瞭解指派或移除角色指派 [連結](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>為您的安全性主體設定 key vault 存取原則

在您授與使用者和應用程式存取金鑰保存庫的存取權之前，請務必瞭解可在金鑰保存庫上執行的不同作業類型。 Key vault 作業有兩種主要類型：管理平面 (也稱為控制平面) 作業和資料平面作業。

下表顯示由管理平面和資料平面所控制之不同作業的數個範例。 變更金鑰保存庫屬性的作業是管理平面作業。 變更或取得金鑰保存庫中所儲存之秘密值的作業是資料平面作業。

|管理平面作業 (範例) |資料平面作業 (範例) |
| --- | --- |
| 建立金鑰保存庫 | 建立金鑰、秘密、憑證
| 刪除 Key Vault | 刪除金鑰、秘密、憑證
| 新增或移除 Key Vault 角色指派 | 列出並取得金鑰、秘密、憑證的值
| 新增或移除 Key Vault 存取原則 | 備份和還原金鑰、秘密、憑證
| 修改 Key Vault 防火牆設定 | 更新金鑰、秘密、憑證
| 修改 Key Vault 復原設定 | 清除或復原虛刪除的金鑰、秘密、憑證
| 修改 Key Vault 診斷記錄設定

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>管理平面存取 & Azure Active Directory 角色指派

Azure Active Directory 角色指派會授與存取權，以在金鑰保存庫上執行管理平面作業。 這項存取通常會授與給使用者，而不是應用程式。 您可以藉由變更使用者的角色指派，來限制使用者可以執行的管理平面作業。

例如，將使用者 Key Vault 讀者角色指派給使用者，將可讓他們查看您的金鑰保存庫（例如存取原則）的屬性，但不允許他們進行任何變更。 將使用者指派為擁有者角色，可讓他們以完整許可權存取金鑰保存庫管理平面設定。

角色指派是在 key vault 存取控制中控制 (IAM) 分頁。 如果您想要讓特定使用者存取成為讀取者或多個金鑰保存庫資源的系統管理員，您可以在保存庫、資源群組或訂用帳戶層級建立角色指派，並將角色指派新增至指派範圍內的所有資源。

在金鑰保存庫中儲存的金鑰、秘密和憑證的資料平面存取權或存取權，可以透過下列兩種方式之一來新增。

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>資料平面存取選項1：傳統 Key Vault 存取原則

Key vault 存取原則會將存取權授與使用者和應用程式，以在金鑰保存庫上執行資料平面作業。

> [!NOTE]
> 此存取模型與 key vault RBAC (選項 2) 不相容，如下所述。 您必須選擇一個。 當您按一下金鑰保存庫的 [存取原則] 索引標籤時，您將有機會進行這項選擇。

傳統存取原則是細微的，這表示您可以允許或拒絕每個個別使用者或應用程式在金鑰保存庫內執行個別作業的能力。 以下是一些範例：

* 安全性主體1可以執行任何金鑰操作，但不允許執行任何秘密或憑證作業。
* 安全性主體2可以列出和讀取所有金鑰、秘密和憑證，但無法執行任何建立、刪除或更新作業。
* 安全性主體3可以備份和還原所有秘密，但無法讀取秘密本身的值。

不過，傳統存取原則不允許依物件層級的許可權，而且指派的許可權會套用至個別金鑰保存庫的範圍。 例如，如果您將「秘密取得」存取原則許可權授與特定金鑰保存庫中的安全性主體，則安全性主體能夠取得該特定金鑰保存庫中的所有秘密。 不過，此「取得秘密」許可權不會自動延伸至其他金鑰保存庫，且必須明確指派。

> [!IMPORTANT]
> 傳統金鑰保存庫存取原則和 Azure Active Directory 角色指派彼此獨立。 在訂用帳戶層級指派安全性主體的「參與者」角色，並不會自動讓安全性主體能夠在訂用帳戶範圍內的每個金鑰保存庫上執行資料平面作業。 您仍然必須授與安全性主體，或授與本身存取原則許可權，以執行資料平面作業。

### <a name="data-plane-access-option-2--key-vault-rbac-preview"></a>資料平面存取選項2： Key Vault RBAC (預覽) 

將存取權授與金鑰保存庫資料平面的新方法，是透過 key vault 角色型存取控制 (RBAC) 。

> [!NOTE]
> 此存取模型與上面顯示的金鑰保存庫傳統存取原則不相容。 您必須選擇一個。 當您按一下金鑰保存庫的 [存取原則] 索引標籤時，您將有機會進行這項選擇。

Key Vault 角色指派是一組 Azure 內建的角色指派，其中包含用來存取金鑰、秘密和憑證的一般許可權集。 此許可權模型也會啟用傳統金鑰保存庫存取原則模型中無法使用的其他功能。

* 您可以允許使用者在訂用帳戶、資源群組或個別金鑰保存庫層級指派這些角色，以大規模管理 RBAC 許可權。 使用者將擁有 RBAC 指派範圍內所有金鑰保存庫的資料平面許可權。 這樣就不需要針對每個金鑰保存庫的每個使用者/應用程式指派個別存取原則許可權。

* RBAC 許可權與 Privileged Identity Management 或 PIM 相容。 這可讓您為特殊許可權角色（例如 Key Vault 系統管理員）設定即時存取控制。 這是最佳的安全性作法，而且會藉由消除對金鑰保存庫的持續存取權，遵循最低許可權的主體。

* RBAC 許可權與每個物件的細微許可權相容，因此您可以限制使用者只能對某些金鑰保存庫物件執行作業。 這可讓多個應用程式共用單一金鑰保存庫，同時仍然隔離應用程式之間的存取。

若要深入瞭解 Key Vault RBAC，請參閱下列檔：

* Azure Key Vault RBAC [連結](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)
* Azure Key Vault RBAC 角色 (預覽) [連結](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-administrator-preview)

## <a name="configure-key-vault-firewall"></a>設定 Key Vault 防火牆

根據預設，金鑰保存庫會允許來自公用網際網路的流量透過公用端點傳送至您的金鑰保存庫。 針對額外的安全性層級，您可以設定 Azure Key Vault 防火牆，以限制金鑰保存庫公用端點的存取權。

若要啟用 key vault 防火牆，請按一下金鑰保存庫入口網站中的 [網路功能] 索引標籤，然後選取允許存取的選項按鈕：「私人端點和選取的網路」。 如果您選擇啟用金鑰保存庫防火牆，則您可以透過金鑰保存庫防火牆來允許流量。

* 將 IPv4 位址新增至 key vault 防火牆允許清單。 此選項最適用于具有靜態 IP 位址的應用程式。

* 將虛擬網路新增至金鑰保存庫防火牆。 此選項最適用于具有動態 IP 位址的 Azure 資源，例如虛擬機器。 您可以將 Azure 資源新增至虛擬網路，並將虛擬網路新增至 key vault 防火牆允許清單。 此選項會使用服務端點，這是虛擬網路內的私人 IP 位址。 這可提供額外一層保護，因此 key vault 與您虛擬網路之間的流量會透過公用網際網路路由傳送。 若要深入瞭解服務端點，請參閱下列檔。 [連結](https://docs.microsoft.com/azure/key-vault/general/network-security)

* 將私人連結連接新增至金鑰保存庫。 此選項會將您的虛擬網路直接連線到 key vault 的特定實例，有效地將您的金鑰保存庫帶入您的虛擬網路中。 若要深入瞭解如何設定金鑰保存庫的私人端點連線，請參閱下列 [連結](https://docs.microsoft.com/azure/key-vault/general/private-link-service)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>測試您的服務主體存取金鑰保存庫的能力

遵循上述所有步驟之後，您就可以從金鑰保存庫設定和取出秘密。

### <a name="authentication-process-for-users-examples"></a>使用者的驗證程式 (範例) 

* 使用者可以登入 Azure 入口網站來使用 key vault。 [Key Vault 入口網站快速入門](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)

* 使用者可以使用 Azure CLI 來使用金鑰保存庫。 [Key Vault Azure CLI 快速入門](https://docs.microsoft.com/azure/key-vault/general/quick-create-cli)

* 使用者可以使用 Azure PowerShell 來使用金鑰保存庫。 [Key Vault Azure PowerShell 快速入門](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>應用程式或服務的 Azure Active Directory 驗證程式 (範例) 

* 應用程式會在函式中提供用戶端密碼和用戶端識別碼，以取得 Azure Active Directory 的權杖。 

* 應用程式會提供憑證來取得 Azure Active Directory 的權杖。 

* Azure 資源會使用 MSI 驗證來取得 Azure Active Directory 的權杖。 

* 深入瞭解 MSI 驗證 [連結](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

### <a name="authentication-process-for-application-python-example"></a>應用程式 (Python 範例) 的驗證程式

使用下列程式碼範例，測試您的應用程式是否可以使用您設定的服務主體，從金鑰保存庫中擷取秘密。

>[!NOTE]
>此範例僅供示範和測試之用。 **請勿在生產環境中使用用戶端秘密驗證** 這不是安全的設計作法。 您應使用用戶端憑證或 MSI 驗證做為最佳作法。

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIEND SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解金鑰保存庫驗證的詳細資訊，請參閱下列檔。 [Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/general/authentication)
