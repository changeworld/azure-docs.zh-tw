---
title: 常見問題-Azure Key Vault 憑證匯入
description: 常見問題-Azure Key Vault 憑證匯入
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 402672d8eeaae8a5097e2ab2905997eb1f646ad6
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056341"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>常見問題-Azure Key Vault 憑證匯入

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>如何在 Azure Key Vault 中匯入憑證？

匯入憑證–針對匯入作業，Azure 金鑰保存庫接受兩種憑證格式 PEM 和 PFX。 雖然只有具有公開部分的 PEM 檔案，Azure key vault 才需要，而且只接受檔案資料夾上的 PEM 或 PFX，以及具有私密金鑰的。 遵循[教學課程以匯入憑證](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>將受密碼保護的憑證匯入金鑰保存庫並下載之後，我就無法看到與憑證相關聯的密碼嗎？
    
在儲存體到金鑰保存庫後上傳的受保護憑證不會儲存其相關聯的密碼。 此密碼只需在匯入作業期間使用一次。 雖然這是依設計的概念，但您一律可以取得憑證作為秘密，並從 Base64 轉換成 PFX 透過[Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)新增先前的密碼。

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>如何解決「錯誤的參數錯誤」？ 可在金鑰保存庫中匯入的支援憑證格式為何？

當您匯入憑證時，您必須確定該金鑰包含在檔案本身。 如果您使用不同的格式來個別擁有私密金鑰，就必須結合該金鑰與憑證。 有些憑證授權單位單位提供不同格式的憑證，因此在匯入憑證之前，請確定它們是在 pem 或 .pfx 格式中，而且使用的金鑰是 RSA 或 ECC。 如需瞭解[憑證需求](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support)和[憑證金鑰需求](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)，請參閱下列各項。

###  <a name="can-i-import-certificate-using-arm-template"></a>我可以使用 ARM 範本匯入憑證嗎？

不可以，您無法使用 ARM 範本執行 certifiate 作業。 建議的解決方法是在 API 或 CLI 或 PowerShell 中使用憑證匯入方法。 如果您有現有憑證，則可以將它匯入為秘密。

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>透過入口網站匯入憑證時出現「發生錯誤」的錯誤訊息。 我該如何進一步進行調查？
    
若要查看更具描述性的錯誤，請透過[Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)或[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)匯入憑證檔案。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>如何解決「錯誤類型：拒絕存取或使用者未經授權匯入憑證」？
    
進行此作業需要憑證/匯入權限。 瀏覽至 Key Vault 的所在位置，您必須在存取原則下，將適當的權限授與使用者。 流覽至 [Key Vault> 存取原則] > [新增存取原則] > 選取 [憑證許可權] (或您想要許可權) > 主體] > 搜尋，然後新增使用者的電子郵件。 [深入瞭解憑證相關的存取原則](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>如何解決「錯誤類型：建立憑證時發生衝突」？
    
憑證名稱必須為唯一。 具有相同名稱的憑證可能會處於虛刪除狀態，也就是根據 Azure key vault 中的[憑證組成](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)，如果 Key Vault 中有另一個金鑰或密碼與您嘗試為憑證指定的名稱相同，則會失敗，而且您將需要移除該金鑰或密碼，或為您的憑證使用不同的名稱。 [查看已刪除的憑證](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>為什麼會收到「錯誤類型：字元長度太長」？
    
* 憑證主體名稱長度的字元限制為 200 char
* 憑證密碼長度的字元限制為 200 char

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>我可以在 Azure Key vault 中匯入過期的憑證嗎？
    
否，過期的 PFX 憑證不會匯入 Azure Key Vault。

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>如何將我的憑證轉換成適當的格式？

您可以要求您的憑證授權單位單位，以所需的格式提供憑證，另外還有協力廠商工具可協助您轉換成適當的格式，不過，Microsoft 將無法再進一步建議如何取得所需格式的憑證。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>我可以從非夥伴 Ca 匯入憑證嗎？
是，您可以從任何 CA 匯入憑證，但 key vault 將無法自動更新這些憑證。 您將能夠設定電子郵件通知，以取得憑證到期的通知。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>如果我從合作夥伴 CA 匯入憑證，自動續約功能仍然可以使用嗎？
是，您必須確定在上傳之後，您會在憑證的發佈原則中指定 autorotation。 此外，變更將會反映到下一個週期或憑證版本。

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>無法看到匯入 Key Vault 的 App Service 憑證嗎？ 
如果成功匯入憑證，請在 [秘密] 分頁底下查看。


## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 憑證](/azure/key-vault/certificates/about-certificates)
