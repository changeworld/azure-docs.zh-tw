---
title: 常見問題-Azure Key Vault 憑證匯入
description: 取得匯入 Azure Key Vault 憑證之常見問題的解答。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: d7d34b61e584b63c517b6c0f8af4cb4adcc7fefe
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289507"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>匯入 Azure Key Vault 憑證常見問題

本文將回答有關匯入 Azure Key Vault 憑證的常見問題。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>如何在 Azure Key Vault 中匯入憑證？

若為憑證匯入作業，Azure Key Vault 接受兩種憑證檔案格式： PEM 和 PFX。 雖然只有具有公用部分的 PEM 檔案，Key Vault 要求並只接受具有私密金鑰的 PEM 或 PFX 檔案。 如需詳細資訊，請參閱 [將憑證匯入 Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)。

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>將受密碼保護的憑證匯入 Key Vault 然後下載之後，為什麼看不到與其相關聯的密碼？
    
在 Key Vault 中匯入和保護憑證之後，就不會儲存其相關聯的密碼。 在匯入作業期間，密碼只需要一次。 這是設計的，但您一律可以取得憑證作為秘密，並透過 [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)新增密碼，將其從 Base64 轉換成 PFX。

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>如何解決「錯誤參數」錯誤？ 匯入 Key Vault 所支援的憑證格式有哪些？

當您匯入憑證時，您必須確定該金鑰包含在檔案中。 如果您將私密金鑰以不同的格式分開儲存，您需要將金鑰與憑證結合。 某些憑證授權單位單位 (Ca) 提供其他格式的憑證。 因此，在匯入憑證之前，請確定它是 PEM 或 PFX 檔案格式，而且金鑰使用 Rivest – Shamir – Adleman (RSA) 或橢圓曲線密碼編譯 (ECC) 加密。 

如需詳細資訊，請參閱 [憑證需求](./certificate-scenarios.md#formats-of-import-we-support) 和 [憑證金鑰需求](../keys/about-keys.md)。

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>我可以使用 ARM 範本匯入憑證嗎？

否，您無法使用 Azure Resource Manager (ARM) 範本來執行憑證作業。 建議的解決方法是使用 Azure API 中的憑證匯入方法、Azure CLI 或 PowerShell。 如果您有現有的憑證，您可以將它匯入為秘密。

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>當我透過 Azure 入口網站匯入憑證時，我收到「發生錯誤」錯誤。 如何進一步調查？
    
若要查看更具描述性的錯誤，請使用 [Azure CLI](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) 或 [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)匯入憑證檔案。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>如何解決「錯誤類型：拒絕存取或使用者未經授權匯入憑證」？
    
匯入作業會要求您授與使用者在存取原則下匯入憑證的許可權。 若要這樣做，請移至您的金鑰保存庫，選取 [ **存取** 原則  >  **新增存取原則** ]  >  ，選取 [ **憑證許可權**  >  **主體** ]，搜尋使用者，然後新增使用者的電子郵件地址。 

如需與憑證相關的存取原則的詳細資訊，請參閱 [關於 Azure Key Vault 憑證](./about-certificates.md#certificate-access-control)。


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>如何解決「錯誤類型：建立憑證時的衝突」？
    
每個憑證名稱都必須是唯一的。 具有相同名稱的憑證可能會處於已虛刪除的狀態。 此外，根據憑證的 [組成](./about-certificates.md#composition-of-a-certificate)，建立新的憑證時，會建立具有相同名稱的可定址秘密，因此如果金鑰保存庫中有另一個金鑰或秘密與您嘗試為憑證指定的名稱相同，則憑證建立將會失敗，而且您必須移除該金鑰或密碼，或為您的憑證使用不同的名稱。 

如需詳細資訊，請參閱 [取得已刪除的憑證](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)作業。

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>為什麼我會收到「錯誤類型：字元長度太長」？
此錯誤可能是由下列兩個原因所造成：    
* 憑證主體名稱的限制為200個字元。
* 憑證密碼的限制為200個字元。

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>我可以將過期的憑證匯入 Azure Key Vault 嗎？
    
否，過期的 PFX 憑證無法匯入 Key Vault。

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>我要如何將憑證轉換成正確的格式？

您可以要求您的 CA 以所需的格式提供憑證。 另外還有協力廠商工具，可協助您將憑證轉換為適當的格式。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>我可以從非夥伴 Ca 匯入憑證嗎？
是的，您可以從任何 CA 匯入憑證，但您的金鑰保存庫將無法自動更新憑證。 您可以設定提醒以收到憑證到期的通知。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>如果我從夥伴 CA 匯入憑證，自動更新功能是否仍然有效？
是。 上傳憑證之後，請務必在憑證的發佈原則中指定 autorotation。 在下一個週期或憑證版本發行之前，您的設定仍會保持有效。

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>為什麼我看不到匯入 Key Vault 的 App Service 憑證？ 
如果您已成功匯入憑證，您應該可以前往 [ **秘密** ] 窗格來確認憑證。


## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 憑證](./about-certificates.md)