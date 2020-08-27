---
title: 在程式碼中使用 TLS/SSL 憑證
description: 瞭解如何在您的程式碼中使用用戶端憑證。 使用用戶端憑證來驗證遠端資源，或使用它們來執行密碼編譯工作。
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b62352d09419de11135f4d7a2740e0e74b80255d
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962123"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>在 Azure App Service 的程式碼中使用 TLS/SSL 憑證

在您的應用程式程式碼中，您可以存取 [新增至 App Service 的公用或私用憑證](configure-ssl-certificate.md)。 您的應用程式程式碼可作為用戶端，並存取需要憑證驗證的外部服務，或可能需要執行密碼編譯工作。 本操作指南說明如何在您的應用程式程式碼中使用公用或私用憑證。

在您的程式碼中使用憑證的這種方法會利用 App Service 中的 TLS 功能，要求您的應用程式必須在 **基本** 層或更高版本中。 如果您的應用程式是 **免費** 或 **共用** 層，則可以將 [憑證檔案包含在應用程式存放庫中](#load-certificate-from-file)。

當您讓 App Service 管理 TLS/SSL 憑證時，您可以個別維護憑證和應用程式程式碼，並保護您的敏感性資料。

## <a name="prerequisites"></a>Prerequisites

若要遵循本操作說明指南：

- [建立 App Service 應用程式](./index.yml)
- [將憑證新增至您的應用程式](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>尋找指紋

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務] > \<app-name>。

從應用程式的左側導覽中，選取 [ **TLS/SSL 設定**]，然後選取 [ **私密金鑰憑證 ( .pfx) ** 或 [ **公開金鑰憑證] ( .cer) **。

尋找您要使用的憑證，然後複製憑證指紋。

![複製憑證指紋](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>讓憑證可供存取

若要在您的應用程式程式碼中存取憑證，請 `WEBSITE_LOAD_CERTIFICATES` 在 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中執行下列命令，以將其指紋新增至應用程式設定：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要讓所有憑證都可供存取，請將值設定為 `*` 。

## <a name="load-certificate-in-windows-apps"></a>在 Windows 應用程式中載入憑證

`WEBSITE_LOAD_CERTIFICATES`應用程式設定可讓您在 windows 憑證存放區中的 windows 裝載應用程式存取指定的憑證，而該位置取決於[定價層](overview-hosting-plans.md)：

- **隔離** 層-在 [本機 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)中。 
- 所有其他層級- [目前的 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)。

在 c # 程式碼中，您會透過憑證指紋來存取憑證。 下列程式碼會載入指紋為 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的憑證。

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

在 JAVA 程式碼中，您可以使用 [主體一般名稱] 欄位從 "Windows-MY" 存放區存取憑證 (查看 [公開金鑰憑證](https://en.wikipedia.org/wiki/Public_key_certificate)) 。 下列程式碼說明如何載入私密金鑰憑證：

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

針對不支援或未提供 Windows 憑證存放區支援的語言，請參閱 [從檔案載入憑證](#load-certificate-from-file)。

## <a name="load-certificate-in-linux-apps"></a>在 Linux 應用程式中載入憑證

`WEBSITE_LOAD_CERTIFICATES`應用程式設定可讓您的 Linux 託管應用程式存取指定的憑證 (包括) 為檔案的自訂容器應用程式。 這些檔案會在下列目錄中找到：

- 私用憑證- `/var/ssl/private` ( 檔案 `.p12`) 
- 公開憑證- `/var/ssl/certs` ( 檔案 `.der`) 

憑證檔案名是憑證指紋。 下列 c # 程式碼說明如何在 Linux 應用程式中載入公開憑證。

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要瞭解如何從 Node.js、PHP、Python、JAVA 或 Ruby 中的檔案載入 TLS/SSL 憑證，請參閱個別語言或 web 平臺的檔。

## <a name="load-certificate-from-file"></a>從檔案載入憑證

例如，如果您需要載入您手動上傳的憑證檔案，最好是使用 [FTPS](deploy-ftp.md) 而非 [Git](deploy-local-git.md)來上傳憑證。 您應該將機密資料（例如私用憑證）保存在原始檔控制之外。

> [!NOTE]
> 即使您從檔案載入憑證，Windows 上的 ASP.NET 和 ASP.NET Core 也必須存取證書存儲。 若要在 Windows .NET 應用程式中載入憑證檔案，請在 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中載入目前的使用者設定檔，並使用下列命令：
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 在您的程式碼中使用憑證的這種方法會利用 App Service 中的 TLS 功能，要求您的應用程式必須在 **基本** 層或更高版本中。

下列 c # 範例會從應用程式中的相對路徑載入公用憑證：

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要瞭解如何從 Node.js、PHP、Python、JAVA 或 Ruby 中的檔案載入 TLS/SSL 憑證，請參閱個別語言或 web 平臺的檔。

## <a name="more-resources"></a>其他資源

* [在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常見問題集：App Service 憑證](./faq-configuration-and-management.md)