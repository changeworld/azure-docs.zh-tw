---
title: 使用自訂根 CA 生成自簽章憑證
titleSuffix: Azure Application Gateway
description: 瞭解如何使用自訂根 CA 生成 Azure 應用程式閘道自簽章憑證
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0447e87fd8685188af8008995ba938092f2b87fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293602"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自訂根 CA 生成 Azure 應用程式閘道自簽章憑證

應用程式閘道 v2 SKU 引入了使用受信任的根憑證來允許後端伺服器。 這將刪除 v1 SKU 中所需的身份驗證憑證。 *根憑證*是 Base-64 編碼的 X.509（。CER） 從後端憑證服務器格式化根憑證。 它標識頒發伺服器憑證的根憑證授權單位 （CA），然後伺服器憑證用於 SSL 通信。

如果網站由知名 CA（例如 GoDaddy 或 DigiCert）簽名，則應用程式閘道預設信任網站的證書。 在這種情況下，您不需要顯式上載根憑證。 有關詳細資訊，請參閱[SSL 終止概述以及使用應用程式閘道端到端 SSL。](ssl-overview.md) 但是，如果您有開發/測試環境，並且不想購買經過驗證的 CA 簽章憑證，則可以創建自己的自訂 CA 並使用它創建自簽章憑證。 

> [!NOTE]
> 預設情況下，自簽章憑證不受信任的，並且可能難以維護。 此外，他們可能使用過時的雜湊和密碼套件，可能不強。 為了更好地安全，購買由知名憑證授權單位簽名的證書。

在本文中，您將了解如何：

- 創建您自己的自訂憑證授權單位
- 創建由自訂 CA 簽名的自簽章憑證
- 將自簽名的根憑證上載到應用程式閘道以驗證後端伺服器

## <a name="prerequisites"></a>Prerequisites

- **[在](https://www.openssl.org/)運行 Windows 或 Linux 的電腦上打開 SSL** 

   雖然可能有可用於證書管理的其他工具，但本教程使用 OpenSSL。 您可以找到與許多 Linux 發行版本（如 Ubuntu）捆綁的 OpenSSL。
- **Web 服務器**

   例如，Apache、IIS 或 NGINX 來測試憑證。

- **應用程式閘道 v2 SKU**
   
  如果沒有現有的應用程式閘道，請參閱[快速入門：使用 Azure 應用程式閘道 - Azure 門戶直接進行 Web 流量](quick-create-portal.md)。

## <a name="create-a-root-ca-certificate"></a>創建根 CA 憑證

使用 OpenSSL 創建根 CA 憑證。

### <a name="create-the-root-key"></a>創建根鍵

1. 登錄到安裝 OpenSSL 的電腦並運行以下命令。 這將創建受密碼保護的金鑰。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示符時，鍵入強式密碼。 例如，使用大寫、小寫、數位和符號至少九個字元。

### <a name="create-a-root-certificate-and-self-sign-it"></a>創建根憑證並自簽名

1. 使用以下命令生成 csr 和證書。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   前面的命令創建根憑證。 您將使用它對伺服器憑證進行簽名。

1. 出現提示時，鍵入根鍵的密碼，以及自訂 CA 的組織資訊，如國家/地區、州、組織、OU 和完全限定的功能變數名稱（這是頒發者域）。

   ![創建根憑證](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>創建伺服器憑證

接下來，您將使用 OpenSSL 創建伺服器憑證。

### <a name="create-the-certificates-key"></a>創建證書的金鑰

使用以下命令生成伺服器憑證的金鑰。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>創建 CSR（證書簽名請求）

CSR 是請求證書時提供給 CA 的公開金鑰。 CA 為此特定請求頒發證書。

> [!NOTE]
> 伺服器憑證的 CN（通用名稱）必須與頒發者域不同。 例如，在這種情況下，頒發機構的 CN 是`www.contoso.com`，伺服器憑證的 CN 為`www.fabrikam.com`。


1. 使用以下命令生成 CSR：

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出現提示時，鍵入根鍵的密碼以及自訂 CA 的組織資訊：國家/地區、州、組織、OU 和完全限定的功能變數名稱。 這是網站的功能變數名稱，它應該不同于發行人。

   ![伺服器憑證](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>使用 CSR 和金鑰生成證書，然後使用 CA 的根金鑰進行簽名

1. 使用以下命令創建證書：

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>驗證新創建的證書

1. 使用以下命令列印 CRT 檔的輸出並驗證其內容：

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![證書驗證](media/self-signed-certificates/verify-cert.png)

1. 驗證目錄中的檔，並確保具有以下檔：

   - contoso.crt
   - contoso.key
   - 法布裡卡姆.crt
   - 法布裡卡姆.鍵

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>在 Web 服務器的 SSL 設置中配置證書

在 Web 服務器中，使用 fabrikam.crt 和 fabrikam.key 檔配置 SSL。 如果 Web 服務器無法獲取兩個檔，則可以使用 OpenSSL 命令將它們合併到單個 .pem 或 .pfx 檔中。

### <a name="iis"></a>IIS

有關如何在 IIS 上導入證書並將其上載為伺服器憑證的說明，請參閱[操作操作：在 Windows Server 2003 中的 Web 服務器上安裝導入的證書](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)。

有關 SSL 綁定說明，請參閱[如何在 IIS 7 上設置 SSL。](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)

### <a name="apache"></a>Apache

以下配置是為 Apache 中的[SSL 配置的示例虛擬主機](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts)：

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

以下配置是具有 SSL 配置的[NGINX 伺服器塊](https://nginx.org/docs/http/configuring_https_servers.html)示例：

![帶 SSL 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>訪問伺服器以驗證配置

1. 將根憑證添加到電腦的受根信任存儲。 訪問網站時，請確保在瀏覽器中看到整個憑證連結。

   ![受信任的根憑證](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假定 DNS 已配置為將 Web 服務器名稱（在此示例中為 www.fabrikam.com）指向 Web 服務器的 IP 位址。 如果沒有，您可以編輯[主機檔](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)以解決該名稱。
1. 流覽到您的網站，然後按一下瀏覽器位址框上的鎖定圖示以驗證網站和證書資訊。

## <a name="verify-the-configuration-with-openssl"></a>使用 OpenSSL 驗證配置

或者，您可以使用 OpenSSL 驗證憑證。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![打開 SSL 憑證驗證](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>將根憑證上載到應用程式閘道的 HTTP 設置

要在應用程式閘道中上載證書，必須將 .crt 證書匯出到編碼的 .cer 格式 Base-64 中。 由於 .crt 已包含 base-64 編碼格式中的公開金鑰，只需將檔副檔名從 .crt 重命名到 .cer。 

### <a name="azure-portal"></a>Azure 入口網站

要從門戶上載受信任的根憑證，請選擇 HTTP**設置**並選擇**HTTPS**協定。

![使用門戶添加證書](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者，您可以使用 Azure CLI 或 Azure PowerShell 上載根憑證。 以下代碼是 Azure PowerShell 示例。

> [!NOTE]
> 以下示例將受信任的根憑證添加到應用程式閘道，創建新的 HTTP 設置並添加新規則，前提是後端池和攔截器已經存在。

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
## will be used to verify the backend server's certificate. Note that SSL handshake will
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
### <a name="verify-the-application-gateway-backend-health"></a>驗證應用程式閘道後端運行狀況

1. 按一下應用程式閘道的**後端運行狀況**視圖，檢查探測器是否正常。
1.    您應該看到 HTTPS 探測器的狀態**正常**。

    ![HTTPS 探頭](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>後續步驟

要瞭解有關應用程式閘道中的 SSL_TLS 的詳細資訊，請參閱[SSL 終止概述以及使用應用程式閘道端到端 SSL。](ssl-overview.md)

