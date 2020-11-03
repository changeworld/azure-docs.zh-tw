---
title: 關於 Azure Key Vault 憑證更新
description: 本文討論如何更新 Azure Key Vault 憑證。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 0720e6b55cec8150eea9d41ca89b2c9b21a0bc94
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287694"
---
# <a name="renew-your-azure-key-vault-certificates"></a>更新 Azure Key Vault 憑證

使用 Azure Key Vault，您可以輕鬆地佈建、管理及部署網路的數位憑證，以及啟用應用程式的安全通訊。 如需更多有關憑證的一般資訊，請參閱[有關 Azure Key Vault 憑證](./about-certificates.md)。

使用短期憑證或增加憑證輪替的頻率，您可以協助防止未經授權的使用者存取您的應用程式。

本文討論如何更新您的 Azure Key Vault 憑證。

## <a name="get-notified-about-certificate-expiration"></a>取得憑證到期的通知
若要取得憑證生命事件的通知，您需要新增憑證連絡人。 憑證連絡人中包含連絡資訊，用來傳送由憑證存留期事件觸發的通知。 連絡人資訊會由金鑰保存庫中的所有憑證共用。 若金鑰保存庫中發生任何憑證事件，系統會將通知傳送給所有指定的連絡人。

### <a name="steps-to-set-certificate-notifications"></a>設定憑證通知的步驟：
首先，將憑證連絡人新增至您的金鑰保存庫。 您可以使用 Azure 入口網站或 PowerShell Cmdlet [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0) 來新增。

再來，設定您想收到憑證到期通知的時間。 若要設定憑證的生命週期屬性，請參閱 [Key Vault 中的設定憑證自動輪替](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)。

如果將憑證的原則設定為自動更新，則發生下列事件時會傳送通知。

- 憑證更新之前
- 在憑證更新之後，指出是否已成功更新憑證，或是如果發生錯誤，需要手動更新憑證。  

  如果憑證的原則設定為以手動方式更新 (僅限電子郵件)，則會在需更新憑證時傳送通知。  

在 Key Vault 中，有三種憑證類別：
-   使用整合式憑證授權單位 (CA) 建立的憑證，例如 DigiCert 或透過 GlobalSign
-   使用未整合 CA 建立的憑證
-   自我簽署憑證

## <a name="renew-an-integrated-ca-certificate"></a>更新整合的 CA 憑證 
Azure Key Vault 會處理受信任的 Microsoft 憑證授權單位 DigiCert 和 GlobalSign 所發行的憑證端對端維護。 了解如何[使用 Key Vault 整合受信任的 CA ](./how-to-integrate-certificate-authority.md)。

## <a name="renew-a-nonintegrated-ca-certificate"></a>更新未整合的 CA 憑證 
藉由使用 Azure Key Vault，您可以從任何 CA 匯入憑證，這可讓您與數個 Azure 資源整合並輕鬆部署。 如果您擔心無法追蹤憑證到期日，或發生憑證已過期的情況，金鑰保存庫可協助您掌握最新情況。 針對未整合的 CA 憑證，金鑰保存庫可讓您設定即將到期的電子郵件通知。 您也可以設定有多個使用者會收到這類通知。

> [!IMPORTANT]
> 憑證是已建立版本的物件。 如果目前的版本即將到期，您必須建立新的版本。 在概念上，每個新版本都是由金鑰所組成的新憑證，也是將該金鑰繫結至身分識別的 Blob。 使用未合作 CA 時，金鑰保存庫會產生金鑰鍵/值組，並傳回憑證簽署要求 (CSR)。

要更新未整合的 CA 憑證，請：

1. 登入 Azure 入口網站，然後開啟您要更新的憑證。
1. 在憑證窗格中，選取 [新版本]。
1. 選取 **憑證作業** 。
1. 選取 [下載 CSR]，將 CSR 檔案下載到您的本機磁碟機。
1. 將 CSR 傳送至您選擇的 CA 以簽署要求。
1. 帶回已簽署的要求，然後在同一個憑證作業窗格中選取 [合併 CSR]。

> [!NOTE]
> 請務必將已簽署的 CSR 與您所建立的相同 CSR 要求合併。 否則，金鑰將不會相符。

如需建立新 CSR 的詳細資訊，請參閱[在 Key Vault 中建立和合併 CSR]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)。

## <a name="renew-a-self-signed-certificate"></a>更新自我簽署憑證

Azure Key Vault 也會處理自我簽署憑證的自動更新。 若要深入了解如何變更發佈原則和更新憑證的生命週期屬性，請參閱[在 Key Vault 中設定憑證自動輪替](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate)。

## <a name="troubleshoot"></a>疑難排解
如果在 Azure 入口網站中發出的憑證處於「已停用」狀態，請移至 **憑證作業** 來檢視該憑證的錯誤訊息。

## <a name="frequently-asked-questions"></a>常見問題集

**如何測試憑證的自動輪替功能？**

建立有效性為 **1 個月** 的憑證，然後將輪替的存留期動作設為 **1%** 。 此設定每 7.2 小時會輪替一次憑證。
  
**憑證自動更新後是否會複寫標記？**

是，標記會在自動更新之後複寫。

## <a name="next-steps"></a>後續步驟
*   [將 Key Vault 與 DigiCert 憑證授權單位整合](how-to-integrate-certificate-authority.md)
*   [教學課程：在 Key Vault 中設定憑證自動輪替](tutorial-rotate-certificates.md)