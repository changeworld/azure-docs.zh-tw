---
title: 產生具有自訂根 CA 的自我簽署憑證
titleSuffix: Azure Application Gateway
description: 了解如何使用自訂根 CA 產生 Azure 應用程式閘道的自我簽署憑證
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396935"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自訂根 CA 產生 Azure 應用程式閘道的自我簽署憑證

應用程式閘道 v2 SKU 引進受信任的根憑證用法來允許後端伺服器。 這會移除 v1 SKU 中所需的驗證憑證。 「根憑證」是來自後端憑證伺服器且採用 Base-64 編碼及 X.509(.CER) 格式的根憑證。 其會識別發行伺服器憑證的根憑證授權單位 (CA)，而此伺服器憑證將會用於 TLS/SSL 通訊。

如果您的網站憑證是由知名的 CA (例如，GoDaddy 或 DigiCert) 所簽署，則應用程式閘道會預設為信任該憑證。 在此情況下，您不需要明確上傳根憑證。 如需詳細資訊，請參閱[應用程式閘道的 TLS 終止和端對端 TLS 概觀](ssl-overview.md)。 不過，如果您有開發/測試環境，而不想購買已驗證的 CA 簽署憑證，您可以建立自己的自訂 CA，並用其來建立自我簽署憑證。 

> [!NOTE]
> 自我簽署憑證會預設為不受信任，而且難以維護。 此外，這些憑證可能會使用過期而安全性不足的雜湊和加密套件。 為獲得更好的安全性，請購買由知名憑證授權單位單位簽署的憑證。

在本文中，您將了解如何：

- 建立您自己的自訂憑證授權單位
- 建立由您自訂 CA 簽署的自我簽署憑證
- 將自我簽署的根憑證上傳至應用程式閘道以驗證後端伺服器

## <a name="prerequisites"></a>Prerequisites

- **在執行 Windows 或 Linux 的電腦上具有 [OpenSSL](https://www.openssl.org/)** 

   雖然可能有其他工具可用於管理憑證，但本教學課程使用 OpenSSL。 您可以找到與許多 Linux 發行版本 (例如 Ubuntu) 搭售的 OpenSSL。
- **網頁伺服器**

   例如，用來測試憑證的 Apache、IIS 或 NGINX。

- **應用程式閘道 v2 SKU**
   
  如果您沒有現有的應用程式閘道，請參閱[快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure 入口網站](quick-create-portal.md)。

## <a name="create-a-root-ca-certificate"></a>建立根 CA 憑證

使用 OpenSSL 建立您的根 CA 憑證。

### <a name="create-the-root-key"></a>建立根金鑰

1. 登入已安裝 OpenSSL 的電腦，然後執行下列命令。 這會建立受密碼保護的金鑰。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示字元中，輸入強式密碼。 例如，至少有 9 個字元，並且使用大寫、小寫、數字和符號。

### <a name="create-a-root-certificate-and-self-sign-it"></a>建立根憑證並自我簽署

1. 使用下列命令來產生 csr 和憑證。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   先前的命令會建立根憑證。 您將使用此憑證來簽署您的伺服器憑證。

1. 出現提示時，請輸入根金鑰的密碼，以及自訂 CA 的組織資訊，例如國家/地區、州、組織、OU 和完整網域名稱 (這是簽發者的網域)。

   ![建立根憑證](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>建立伺服器憑證

接下來，您將使用 OpenSSL 來建立伺服器憑證。

### <a name="create-the-certificates-key"></a>建立憑證的金鑰

使用下列命令來產生伺服器憑證的金鑰。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>建立 CSR (憑證簽署要求)

CSR 是在要求憑證時提供給 CA 的公開金鑰。 CA 會為此特定要求發出憑證。

> [!NOTE]
> 伺服器憑證的 CN (一般名稱) 必須不同於簽發者的網域。 例如，在此案例中，簽發者的 CN 是 `www.contoso.com`，而伺服器憑證的 CN 是 `www.fabrikam.com`。


1. 使用下列命令來產生 CSR：

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出現提示時，請輸入根金鑰的密碼，以及自訂 CA 的組織資訊：國家/地區、州、組織、OU 和完整網域名稱。 這是網站的網域，而且應該與簽發者不同。

   ![伺服器憑證](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>以 CSR 和金鑰來產生憑證，並使用 CA 的根金鑰加以簽署

1. 使用下列命令來建立憑證：

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>驗證新建立的憑證

1. 使用下列命令來列印 CRT 檔案的輸出，並驗證其內容：

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![憑證驗證](media/self-signed-certificates/verify-cert.png)

1. 確認目錄中的檔案，並確定您有下列檔案：

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>在 Web 伺服器的 TLS 設定中設定憑證

在您的 Web 伺服器中，使用 fabrikam.crt 和 fabrikam.key 檔案來設定 TLS。 如果您的 Web 伺服器無法接受兩個檔案，您可以使用 OpenSSL 命令，將這兩個檔案合併成單一的 .pem 或 .pfx 檔案。

### <a name="iis"></a>IIS

若要了解如何匯入憑證並將其上傳為 IIS 上的伺服器憑證，請參閱[操作說明：在 Windows Server 2003 中的 Web 服務器上安裝匯入的憑證](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)。

如需 TLS 繫結指示，請參閱[如何在 IIS 7 上設定 SSL](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)。

### <a name="apache"></a>Apache

下列設定是 Apache 中[為 SSL 設定的虛擬主機](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts)範例：

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

下列設定是使用 TLS 設定的 [NGINX 伺服器區塊](https://nginx.org/docs/http/configuring_https_servers.html)範例：

![使用 TLS 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>存取伺服器以驗證設定

1. 將根憑證新增至您機器信任的根存放區。 當您存取網站時，請確定已在瀏覽器中看到整個憑證鏈結。

   ![受信任的根憑證](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假設 DNS 已設定為將 Web 伺服器名稱 (在此範例中為 www.fabrikam.com) 指向 Web 伺服器的 IP 位址。 如果不是，您可以編輯[主機檔案](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)來解析名稱。
1. 瀏覽至您的網站，然後按一下瀏覽器網址方塊上的 [鎖定] 圖示，以確認網站和憑證資訊。

## <a name="verify-the-configuration-with-openssl"></a>使用 OpenSSL 驗證設定

或者，您可以使用 OpenSSL 來驗證憑證。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 憑證驗證](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>將根憑證上傳至應用程式閘道的 HTTP 設定

若要上傳應用程式閘道中的憑證，您必須將 .crt 憑證匯出為 Base-64 編碼的 .cer 格式。 由於 .crt 已包含 Base-64 編碼格式的公開金鑰，因此只要將副檔名從 .crt 重新命名為 .cer 即可。 

### <a name="azure-portal"></a>Azure 入口網站

若要從入口網站上傳受信任的根憑證，請選取 [HTTP 設定]，然後選擇 [HTTPS] 通訊協定。

![使用入口網站新增憑證](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者，您可以使用 Azure CLI 或 Azure PowerShell 來上傳根憑證。 下列程式碼是 Azure PowerShell 範例。

> [!NOTE]
> 下列範例會將受信任的根憑證新增到應用程式閘道，然後建立新的 HTTP 設定並新增規則 (假設後端集區和接聽程式已經存在)。

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>確認應用程式閘道後端的健康情況

1. 按一下應用程式閘道的 [後端健康情況] 檢視，以確認探查狀況是否良好。
1. 您應該會看到 HTTPS 探查的狀態為 **良好** 。

![HTTPS 探查](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>後續步驟

若要深入了解應用程式閘道中的 SSL\TLS，請參閱[應用程式閘道的 TLS 終止和端對端 TLS 概觀](ssl-overview.md)。