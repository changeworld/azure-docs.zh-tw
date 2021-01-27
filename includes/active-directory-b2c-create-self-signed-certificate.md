---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916910"
---
如果您還沒有憑證，可以在本教學課程中使用自我簽署憑證。 自我簽署憑證是憑證授權單位單位)  (CA 所簽署的安全性憑證。 它們不會提供憑證授權單位單位所簽署之憑證的所有安全性保證。 

# <a name="windows"></a>[Windows](#tab/windows)

在 Windows 上，使用 PowerShell 的 [new-selfsignedcertificate](/powershell/module/pkiclient/new-selfsignedcertificate) Cmdlet 來產生憑證。

1. 執行此 PowerShell 命令來產生自我簽署憑證。 針對您的應用程式與 Azure AD B2C 租用戶名稱，適當地修改 `-Subject` 引數。 您也可以調整 `-NotAfter` 日期，為憑證指定不同的到期日。

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. 開啟 [管理使用者憑證] > [目前使用者] > [個人] > [憑證] > [yourappname.yourtenant.onmicrosoft.com]
1. 選取憑證 > [動作] > [所有工作] > [匯出]
1. 選取 [是] > [下一步] > [是，匯出私密金鑰] > [下一步]
1. 接受 [匯出檔案格式] 的預設值
1. 提供憑證的密碼。

為了讓 Azure AD B2C 接受 .pfx 檔案密碼，必須在 Windows 憑證存放區匯出公用程式中使用 TripleDES-SHA1 選項加密密碼，而不是使用 AES256-SHA256。

# <a name="macos"></a>[macOS](#tab/macos)

在 macOS 上，使用 Keychain 存取中的 [憑證助理](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) 來產生憑證。

1. 遵循如何 [在 Mac 的 Keychain 存取中建立自我簽署憑證](https://support.apple.com/guide/keychain-access/kyca8916/mac)的指示。
1. 在 Mac 上的 Keychain Access 應用程式中，選取您建立的憑證。
1. 選擇 [檔案 > 匯出專案]。
1. 選取檔案名以儲存您的憑證。 例如， **自我簽署憑證 p12**。 
1. 在 [ **檔案格式**] 中，選取 [ **個人資訊交換] ( p12)**。
1. 選取 [儲存]。
1. 輸入 **密碼**，然後 **確認** 密碼。
1. 將副檔名取代為 `.pfx` 。 例如， **self-signed-certificate .pfx**。 

---
