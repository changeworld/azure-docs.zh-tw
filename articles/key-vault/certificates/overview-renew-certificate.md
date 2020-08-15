---
title: 關於 Azure Key Vault 憑證更新
description: 關於 Azure Key Vault 憑證更新
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: bbd65cdfc7c8ebbc08b914158577337cc49ec887
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236058"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>關於 Azure Key Vault 憑證更新

Azure Key Vault 可讓您輕鬆地布建、管理及部署網路的數位憑證，以及啟用應用程式的安全通訊。 如需有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

擁有短期憑證或增加憑證輪替的頻率，會限制敵人的損毀範圍。

## <a name="certificate-expiration-notifications"></a>憑證到期通知
首先，請務必將憑證連絡人新增至您的 Key Vault，以便在憑證即將到期時收到通知 (例如。 使用 PowerShell [AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) 秒，設定您想要取得憑證到期通知的時間。 若要設定存留時間動作，請 [在這裡](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

在 key vault 中建立憑證的類別有三種。 本指南將協助您瞭解如何才能達成憑證的更新。
-   使用整合式 CA 建立的憑證 (DigiCert 或透過 globalsign) 
-   使用非整合式 CA 建立的憑證
-   自我簽署憑證

## <a name="renewal-of-integrated-ca-certificate"></a>已整合的 CA 憑證更新 
好消息！ Azure Key vault 會處理由 Microsoft 信任的 Ca （例如 DigiCert 和透過 globalsign）所發行之憑證的端對端維護。 瞭解如何 [整合受信任的 CA 與金鑰保存庫](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)。

## <a name="renewal-of-non-integrated-ca-certificate"></a>更新非整合式 CA 憑證 
Azure key vault 可讓其使用者從任何 CA 匯入憑證，以允許其使用者與數個 Azure 資源整合並讓部署變得更輕鬆。 如果您擔心遺失憑證的追蹤已過期，或發現您的憑證已過期，則會更糟。然後 Key Vault 可以協助您保持最新狀態。 針對非整合式 CA 憑證，key vault 可讓其使用者設定近乎過期的電子郵件通知。 這些通知也可以針對多個使用者進行設定。

現在，若要更新憑證，請務必瞭解 Azure Key Vault 憑證是已建立版本的物件。 如果目前的版本即將到期，您就必須建立新的版本。 在概念上，每個新版本都是由索引鍵所組成的新憑證，以及將該金鑰與身分識別結合的 blob。 當您使用非合作的 CA 時，金鑰保存庫會產生金鑰值組並傳回 CSR。

**Azure 入口網站中要遵循的步驟：-**
1.  開啟您想要更新的憑證。
2.  選取 [憑證] 畫面上的 [ **+ 新增版本** ] 按鈕。
3.  選取**憑證**作業
4.  選取 [ **下載 CSR**]。 這會在您的本機磁片磁碟機上下載一個 csr 檔案。
5.  將 CSR 帶給您選擇的 CA 來簽署要求
6.  帶回已簽署的要求，然後在相同的憑證作業畫面上選取 [ **合併 CSR** ]。

> [!NOTE]
> 請務必將已簽署的 CSR 與已建立的相同 CSR 要求合併，否則金鑰將不會相符。

步驟類似于建立新的憑證，詳細說明請參閱 [這裡]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)。

## <a name="renewal-of-self-signed-certificate"></a>自我簽署憑證的更新

好消息！ Azure 金鑰保存庫也會負責自動更新其使用者的自我簽署憑證。 若要深入瞭解如何變更發佈原則和更新憑證的存留期動作屬性，請 [在這裡](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)閱讀更多資訊。

### <a name="troubleshoot"></a>疑難排解
如果在 Azure 入口網站中發出的憑證處於「已停用」狀態，請繼續查看憑證作業，以查看該憑證的錯誤訊息。

### <a name="frequently-asked-questions"></a>常見問題集
* 如何測試憑證的 autorotation 功能？
  建立有效期為1個月的憑證，然後將 [旋轉的存留時間] 動作設定為1%。 這項設定會將憑證旋轉7.2 小時。
  
* 會在自動更新憑證後複寫標記嗎？
  是，標記會在自動更新後進行複寫。

### <a name="see-also"></a>另請參閱
*   [將 Key Vault 與 DigiCert 憑證授權單位整合](how-to-integrate-certificate-authority.md)
*   [教學課程：在 Key Vault 中設定憑證的自動輪替](tutorial-rotate-certificates.md)
