---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059953"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>建立自我簽署根憑證

您可以使用 New-SelfSignedCertificate Cmdlet 來建立自我簽署的根憑證。 如需其他的參數資訊，請參閱 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

1. 從執行 Windows 10 或 Windows Server 2016 的電腦，以提高的權限開啟 Windows PowerShell 主控台。 這些範例無法在 Azure Cloud Shell 的「試試看」中運作。 您必須在本機執行這些範例。
2. 使用下列範例建立自我簽署的根憑證。 下列範例會建立名為 'P2SRootCert' 的自我簽署的根憑證，其自動安裝在 'Certificates-Current User\Personal\Certificates' 中。 您可以開啟 *certmgr.msc* 或 [管理使用者憑證]** 來檢視憑證。

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. 如果您想要在建立此根憑證之後建立用戶端憑證，請讓 PowerShell 主控台保持開啟。

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>產生用戶端憑證 

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 您可以從自我簽署根憑證產生用戶端憑證，然後匯出及安裝用戶端憑證。 如果未安裝用戶端憑證，則驗證會失敗。 

下列步驟將逐步引導您完成從自我簽署的根憑證產生用戶端憑證。 您可以從相同根憑證產生多個用戶端憑證。 當您使用下列步驟產生用戶端憑證時，用戶端憑證會自動安裝在您用來產生憑證的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您可以匯出憑證。

此範例會使用 New-SelfSignedCertificate Cmdlet 來產生有效期為一年的用戶端憑證。 如需其他的參數資訊 (例如針對用戶端憑證設定不同的到期值)，請參閱 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

### <a name="example-1---powershell-console-session-still-open"></a>範例 1-PowerShell 主控台會話仍然開啟

如果您在建立自我簽署根憑證後沒有關閉 PowerShell 主控台，請使用此範例。 此範例會從上一節的內容繼續，並使用宣告的 '$cert' 變數。 如果您在建立自我簽署根憑證之後關閉 PowerShell 主控台，或是在新的 PowerShell 主控台會話中建立其他用戶端憑證，請使用[範例 2](#ex2)中的步驟。

修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。  如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>範例 2-新的 PowerShell 主控台會話

如果您要建立其他用戶端憑證，或者不是使用您用來建立自我簽署根憑證的相同 PowerShell 工作階段，請使用下列步驟︰

1. 識別安裝在電腦上的自我簽署根憑證。 此 Cmdlet 會傳回安裝於電腦上的憑證清單。

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. 從傳回的清單尋找主體名稱，然後將其旁邊的指紋複製到文字檔。 在下列範例中，有兩個憑證。 CN 名稱是您要從中產生子憑證之自我簽署根憑證的名稱。 在此例中為 'P2SRootCert'。

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. 使用上一個步驟中的指紋，為根憑證宣告一個變數。 將 THUMBPRINT 替換為您要從中產生子憑證之根憑證的指紋。

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   例如，使用上一個步驟中的 P2SRootCert 的指紋，變數會如下所示︰

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. 修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。 如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>匯出根憑證公開金鑰 (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>匯出自我簽署根憑證和私密金鑰來儲存它 (選擇性)

您可能想要匯出自我簽署的根憑證，並將它安全地儲存作為備份。 如有需要，您可以稍後在另一部電腦上安裝這個自我簽署憑證，然後產生更多用戶端憑證。 若要將自我簽署的根憑證匯出為 .pfx，請選取根憑證，然後使用與[匯出用戶端憑證](#clientexport)所述的相同步驟來匯出。

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>匯出用戶端憑證

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
