---
title: 使用自訂根 CA 產生自簽署憑證
titleSuffix: Azure Application Gateway
description: 瞭解如何使用自訂根 CA 產生 Azure 應用程式閘道自簽署憑證
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311947"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自訂根 CA 產生 Azure 應用程式閘道自簽署憑證

應用程式閘道 v2 SKU 引入了使用受信任的根證書來允許後端伺服器。 這將刪除 v1 SKU 中所需的身份驗證證書。 *根證書*是 Base-64 編碼的 X.509(。CER) 從後端憑證伺服器格式化根證書。 它識別頒發伺服器證書的根證書頒發機構 (CA),然後伺服器證書用於 TLS/SSL 通訊。

如果網站由知名 CA(例如 GoDaddy 或 DigiCert)簽名,則應用程式閘道預設信任網站的證書。 在這種情況下,您不需要顯式上載根證書。 有關詳細資訊,請參閱[使用應用程式閘道的 TLS 中止和端到端 TLS 概述](ssl-overview.md)。 但是,如果您有開發/測試環境,並且不想購買經過驗證的 CA 簽名證書,則可以創建自己的自定義 CA 並使用它創建自簽名證書。 

> [!NOTE]
> 默認情況下,自簽名證書不受信任的,並且可能難以維護。 此外,他們可能使用過時的哈希和密碼套件,可能不強。 為了更好地安全,購買由知名證書頒發機構簽名的證書。

在本文中，您將了解如何：

- 建立您自己的自訂憑證發行單位
- 建立由自訂 CA 簽署的自簽署憑證
- 將自簽署的根憑證上傳到應用程式閘道來驗證後端伺服器

## <a name="prerequisites"></a>Prerequisites

- **[在](https://www.openssl.org/)執行 Windows 或 Linux 的電腦上開啟 SSL** 

   雖然可能有可用於證書管理的其他工具,但本教程使用 OpenSSL。 您可以找到與許多 Linux 發行版(如 Ubuntu)捆綁的 OpenSSL。
- **Web 伺服器**

   例如,Apache、IIS 或 NGINX 來測試證書。

- **應用程式閘道 v2 SKU**
   
  如果沒有現有的應用程式閘道,請參閱[快速入門:使用 Azure 應用程式閘道 - Azure 門戶直接進行 Web 流量](quick-create-portal.md)。

## <a name="create-a-root-ca-certificate"></a>建立根憑證

使用 OpenSSL 建立根 CA 憑證。

### <a name="create-the-root-key"></a>建立根鍵

1. 登錄到安裝 OpenSSL 的電腦並執行以下命令。 這將創建受密碼保護的密鑰。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示符時,鍵入強密碼。 例如,使用大寫、小寫、數位和符號至少九個字元。

### <a name="create-a-root-certificate-and-self-sign-it"></a>建立根憑證並自簽署

1. 使用以下命令生成 csr 和證書。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   前面的命令建立根證書。 您將使用它對伺服器證書進行簽名。

1. 出現提示時,鍵入根鍵的密碼,以及自定義 CA 的組織資訊,如國家 / 地區、州、組織、OU 和完全限定的功能變數名稱(這是頒發者域)。

   ![建立根憑證](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>建立伺服器憑證

接下來,您將使用 OpenSSL 建立伺服器證書。

### <a name="create-the-certificates-key"></a>建立憑證的金鑰

使用以下命令生成伺服器證書的密鑰。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>建立 CSR(憑證簽署要求)

CSR 是請求證書時提供給 CA 的公開金鑰。 CA 為此特定請求頒發證書。

> [!NOTE]
> 伺服器證書的 CN(通用名稱)必須與頒發者域不同。 例如,在這種情況下,頒發機構的 CN`www.contoso.com`是 ,伺服器證書`www.fabrikam.com`的 CN 為 。


1. 使用以下指令產生 CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出現提示時,鍵入根鍵的密碼以及自定義 CA 的組織資訊:國家 / 地區、州、組織、OU 和完全限定的功能變數名稱。 這是網站的功能變數名稱,它應該不同於發行人。

   ![伺服器憑證](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>使用 CSR 與金鑰產生憑證,然後使用 CA 的根金鑰簽署

1. 使用以下指令建立憑證:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>驗證新建立的憑證

1. 使用以下指令列印 CRT 檔的輸出並驗證內容:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![憑證驗證](media/self-signed-certificates/verify-cert.png)

1. 驗證目錄中的檔案,並確保具有以下檔案:

   - contoso.crt
   - contoso.key
   - 法布里卡姆.crt
   - 法布里卡姆.鍵

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>在 Web 伺服器的 TLS 設定中設定憑證

在 Web 伺服器中,使用 fabrikam.crt 和 fabrikam.key 檔配置 TLS。 如果 Web 伺服器無法取得兩個檔,則可以使用 OpenSSL 命令將它們合併到單個 .pem 或 .pfx 檔中。

### <a name="iis"></a>IIS

有關如何在 IIS 上匯入憑證並將其上傳為伺服器憑證的說明,請參閱[操作操作:在 Windows Server 2003 中的 Web 伺服器上安裝匯入的憑證](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)。

有關 TLS 綁定說明,請參閱[如何在 IIS 7 上設置 SSL。](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)

### <a name="apache"></a>Apache

以下設定為 Apache 中的[SSL 設定的範例虛擬主機](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts):

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

以下設定是具有 TLS 設定的[NGINX 伺服器區塊](https://nginx.org/docs/http/configuring_https_servers.html)的範例:

![帶 TLS 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>存取伺服器以驗證設定

1. 將根憑證添加到計算機的受信任根儲存。 訪問網站時,請確保在瀏覽器中看到整個證書鏈。

   ![受信任的根憑證](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假定 DNS 已設定為將 Web 伺服器名稱(在此範例中為 www.fabrikam.com)指向 Web 伺服器的 IP 位址。 如果沒有,您可以編輯[主機檔](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)以解決該名稱。
1. 瀏覽到您的網站,然後單擊瀏覽器位址框上的鎖定圖示以驗證網站和證書資訊。

## <a name="verify-the-configuration-with-openssl"></a>使用 OpenSSL 驗證設定

或者,您可以使用 OpenSSL 驗證證書。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![開啟 SSL 憑證驗證](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>將根憑證上載到應用程式閘道的 HTTP 設定

要在應用程式閘道中上傳證書,必須將 .crt 憑證匯出到編碼的 .cer 格式 Base-64 中。 由於 .crt 已包含 base-64 編碼格式中的公開金鑰,只需將檔案副檔名從 .crt 重新命名到 .cer。 

### <a name="azure-portal"></a>Azure 入口網站

要從門戶上載受信任的根證書,請選擇 HTTP**設定**並選擇**HTTPS**協定。

![使用門戶新增憑證](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者,您可以使用 Azure CLI 或 Azure PowerShell 上載根證書。 以下代碼是 Azure PowerShell 示例。

> [!NOTE]
> 以下範例將受信任的根憑證添加到應用程式閘道,創建新的 HTTP 設定並新增新規則,前提是後端池和偵聽器已經存在。

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

### <a name="verify-the-application-gateway-backend-health"></a>驗證應用程式閘道後端執行狀況

1. 單擊應用程序閘道的**後端運行狀況**,檢查探測器是否正常。
1. 您應該看到 HTTPS 偵測器的狀態 **。**

![HTTPS 探頭](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>後續步驟

要瞭解有關應用程式閘道中的 SSL_TLS的詳細資訊,請參閱[TLS 終止概述以及使用應用程式閘道端到端 TLS](ssl-overview.md)的 TLS 概述。

