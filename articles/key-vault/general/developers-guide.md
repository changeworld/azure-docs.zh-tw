---
title: Azure 金鑰保存庫開發人員指南
description: 開發人員可以使用 Azure 金鑰保存庫來管理 Microsoft Azure 環境中的密碼編譯金鑰。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 662c23a29e383800a4591c900e02133c16fa2090
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743313"
---
# <a name="azure-key-vault-developers-guide"></a>Azure 金鑰保存庫開發人員指南

Key Vault 可讓您從應用程式內安全地存取機密資訊︰

- 金鑰、秘密和憑證會受到保護，而不需要自行撰寫程式碼，而且您可以輕鬆地從您的應用程式使用它們。
- 您可以讓客戶擁有及管理他們自己的金鑰、秘密和憑證，以便您專注于提供核心軟體功能。 如此一來，您的應用程式就不會擁有客戶租使用者金鑰、秘密和憑證的責任或潛在責任。
- 您的應用程式可以使用金鑰進行簽署和加密，但可讓您的應用程式從外部管理金鑰。 如需有關金鑰的詳細資訊，請參閱 [關於金鑰](../keys/about-keys.md)
- 您可以管理諸如密碼、存取金鑰、sas 權杖將憑證儲存在 Key Vault 作為秘密的認證，請參閱 [關於秘密](../secrets/about-secrets.md)
- 管理憑證。 如需詳細資訊，請參閱 [關於憑證](../certificates/about-certificates.md)

如需 Azure 金鑰保存庫的一般詳細資訊，請參閱 [什麼是金鑰保存庫？](overview.md)。

## <a name="public-previews"></a>公開預覽

我們會定期發行新 Key Vault 功能的公開預覽。 試用公開預覽功能，讓我們知道您的想法，也就是 azurekeyvault@microsoft.com 我們的意見反應電子郵件地址。

## <a name="creating-and-managing-key-vaults"></a>建立及管理金鑰保存庫

Key Vault 管理（類似于其他 Azure 服務）是透過 Azure Resource Manager 服務來完成。 Azure Resource Manager 是 Azure 的部署和管理服務。 其提供管理層，可讓您建立、更新和刪除您 Azure 帳戶中的資源。 如需詳細資訊，請參閱 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

管理層的存取權是由 [Azure 角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)所控制。 在 Key Vault 中，管理層（也稱為管理或控制平面）可讓您建立和管理金鑰保存庫和其屬性，包括存取原則，而不是在資料平面上管理的金鑰、秘密和憑證。 您可以使用預先定義的 `Key Vault Contributor` 角色，將管理存取權授與 Key Vault。     

**金鑰保存庫管理的 API 和 Sdk：**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[參考](/cli/azure/keyvault)<br>[快速入門](quick-create-cli.md)|[參考](/powershell/module/az.keyvault)<br>[快速入門](quick-create-powershell.md)|[參考](/rest/api/keyvault/)|[參考](/azure/templates/microsoft.keyvault/vaults)|[參考](/dotnet/api/microsoft.azure.management.keyvault)|[參考](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[參考](/java/api/com.microsoft.azure.management.keyvault)|[參考](/javascript/api/@azure/arm-keyvault)|

請參閱安裝套件和原始程式碼的 [用戶端程式庫](client-libraries.md) 。

如需 Key Vault 管理平面的詳細資訊，請參閱 [Key Vault 管理平面](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>在程式碼中驗證 Key Vault

Key Vault 使用 Azure AD 驗證，要求 Azure AD 安全性主體授與存取權。 Azure AD 的安全性主體可能是使用者、應用程式服務主體、 [適用于 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，或任何類型之安全性主體的群組。

### <a name="authentication-best-practices"></a>驗證最佳作法
建議您針對部署至 Azure 的應用程式使用受控識別。 如果您使用的 Azure 服務不支援受控識別，或應用程式是部署在內部部署環境，則有可能的替代方案是 [具有憑證的服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 。 在該案例中，憑證應儲存在 Key Vault 中，並經常輪替。 具有秘密的服務主體可用於開發和測試環境，建議您在本機或 Cloud Shell 使用使用者主體。

Azure 身分識別用戶端程式庫支援上述驗證案例，並與 Key Vault Sdk 整合。 Azure 身分識別程式庫可以跨不同的環境和平臺使用，而不需要變更您的程式碼。 Azure 身分識別也會自動從使用 Azure CLI、Visual Studio、Visual Studio Code 及其他專案登入 Azure 使用者的驗證權杖中取出。 

如需詳細資訊，請參閱 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure 身分識別 SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure 身分識別 SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure 身分識別 SDK JAVA](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure 身分識別 SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>管理金鑰、憑證和秘密

金鑰、秘密和憑證的存取權是由資料平面所控制。 您可以使用本機保存庫存取原則或 RBAC (預覽) 來完成資料平面存取控制。

**金鑰 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[參考](/cli/azure/keyvault/key)<br>[快速入門](../keys/quick-create-cli.md)|[參考](/powershell/module/az.keyvault/)<br>[快速入門](../keys/quick-create-powershell.md)|[參考](/rest/api/keyvault/#key-operations)|N/A|[參考](/dotnet/api/azure.security.keyvault.keys)|[參考](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[快速入門](../keys/quick-create-python.md)|[參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[參考](/javascript/api/@azure/keyvault-keys/)|

**憑證 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[參考](/cli/azure/keyvault/certificate)<br>[快速入門](../certificates/quick-create-cli.md)|[參考](/powershell/module/az.keyvault)<br>[快速入門](../certificates/quick-create-powershell.md)|[參考](/rest/api/keyvault/#certificate-operations)|N/A|[參考](/dotnet/api/azure.security.keyvault.certificates)|[參考](/python/api/overview/azure/keyvault-certificates-readme)<br>[快速入門](../certificates/quick-create-python.md)|[參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[參考](/javascript/api/@azure/keyvault-certificates/)|

**秘密 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[參考](/cli/azure/keyvault/secret)<br>[快速入門](../secrets/quick-create-cli.md)|[參考](/powershell/module/az.keyvault/)<br>[快速入門](../secrets/quick-create-powershell.md)|[參考](/rest/api/keyvault/#secret-operations)|[參考](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[快速入門](../secrets/quick-create-template.md)|[參考](/dotnet/api/azure.security.keyvault.secrets)<br>[快速入門](../secrets/quick-create-net.md)|[參考](/python/api/overview/azure/keyvault-secrets-readme)<br>[快速入門](../secrets/quick-create-python.md)|[參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[快速入門](../secrets/quick-create-java.md)|[參考](/javascript/api/@azure/keyvault-secrets/)<br>[快速入門](../secrets/quick-create-node.md)|

請參閱安裝套件和原始程式碼的 [用戶端程式庫](client-libraries.md) 。

如需 Key Vault 資料平面安全性的詳細資訊，請參閱 [Key Vault 資料平面和存取原則](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) ，以及 [Key Vault 資料平面和 RBAC (預覽) ](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>程式碼範例

如需搭配使用金鑰保存庫和應用程式的完整範例，請參閱︰

- [Azure Key Vault 程式碼範例](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault 的程式碼範例。 

## <a name="how-tos"></a>做法

下列文章和案例提供使用 Azure Key Vault 的工作特定指引：

- [存取防火牆後面的金鑰保存庫](access-behind-firewall.md)若要存取金鑰保存庫，您的金鑰保存庫用戶端應用程式必須能夠存取多個端點，才能使用各種功能。
- 如何從 Key Vault [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)（ [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) ）將憑證部署至 vm-在 Azure 上的 VM 中執行的雲端應用程式需要憑證。 現在應如何讓此憑證進入此 VM？
- [透過 Key Vault 部署 Azure Web 應用程式憑證](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
-  ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [入口網站](assign-access-policy-portal.md)) 指派存取原則。 
- [如何以 CLI 使用金鑰保存庫虛刪除](soft-delete-cli.md)引導您完成金鑰保存庫和各種金鑰保存庫物件的使用和生命週期，並啟用虛刪除。
- [如何在部署期間傳遞安全值 (例如密碼)](../../azure-resource-manager/templates/key-vault-parameter.md) - 當您需要在部署期間傳遞安全值 (例如密碼) 作為參數時，可以將該值儲存為 Azure 金鑰保存庫中的密碼，並在其他資源管理員範本中參考該值。

## <a name="integrated-with-key-vault"></a>與金鑰保存庫整合

這些文章是關於其他可讓我們使用及整合 Key Vault 的案例和服務。

- [使用 Key Vault 進行靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Azure 資訊保護](/azure/information-protection/plan-implement-tenant-key)可讓您管理自己的租用戶金鑰。 例如，您可以管理自己的租用戶金鑰，以符合適用於貴組織的特定規範，而不需 Microsoft 管理您的租用戶金鑰 (預設值)。 管理自己的租用戶金鑰也稱為「自備金鑰」或 BYOK。

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 的概觀和概念

- [Key Vault 虛刪除行為](soft-delete-overview.md)描述一項功能，該功能可復原已刪除的物件，無論是無意或有意刪除的。
- [Key Vault 用戶端節流](overview-throttling.md)可讓您了解節流的基本概念，並提供適用於您應用程式的方法。
- [Key Vault 安全世界](overview-security-worlds.md)描述地區和安全區域之間的關聯性。

## <a name="social"></a>社交

- [Key Vault Blog (金鑰保存庫部落格)](https://aka.ms/kvblog)
- [Key Vault Forum (金鑰保存庫論壇)](https://aka.ms/kvforum)
