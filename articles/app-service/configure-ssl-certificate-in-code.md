---
title: 在代碼中使用 TLS/SSL 憑證
description: 瞭解如何在代碼中使用客戶端證書。 使用用戶端證書對遠端資源進行身份驗證,或使用它們運行加密任務。
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811689"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>在 Azure 應用服務中的代碼中使用 TLS/SSL 憑證

在應用程式代碼中,可以存[取到應用程式服務的公共或私有憑證](configure-ssl-certificate.md)。 你的應用代碼可能充當用戶端並訪問需要證書身份驗證的外部服務,或者可能需要執行加密任務。 本說明指南示範如何在應用程式代碼中使用公共或私有證書。

在代碼中使用證書的此方法使用應用服務中的 TLS 功能,該功能要求你的應用位於**基本**層或以上。 如果你的應用程式處於 **「免費**」或「**共用**」 , 則可以[在套用儲存函式庫中包括憑證檔](#load-certificate-from-file)。

當您讓應用服務管理 TLS/SSL 證書時,您可以分別維護證書和應用程式代碼並保護您的敏感資料。

## <a name="prerequisites"></a>Prerequisites

若要遵循本操作說明指南：

- [建立應用服務應用程式](/azure/app-service/)
- [新增憑證](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>尋找指紋

在<a href="https://portal.azure.com" target="_blank">Azure 門戶</a>中,從左側選單中選擇**應用服務** > **\<應用名稱>**。

從應用的左側導航中,選擇**TLS/SSL 設置**,然後選擇**私鑰證書 (.pfx)** 或**公鑰證書 (.cer)。**

查找要使用的證書並複製指紋。

![複製憑證指紋](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>使憑證可存取

要存取應用程式代碼中的憑證,請透過在<a target="_blank" href="https://shell.azure.com" >雲端外殼</a>中執行`WEBSITE_LOAD_CERTIFICATES`以下指令 ,將其指紋添加到應用設定中:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

要存取所有憑證都存取,請`*`將該值設定為 。

## <a name="load-certificate-in-windows-apps"></a>在 Windows 應用中載入憑證

套`WEBSITE_LOAD_CERTIFICATES`用設定讓 Windows 託管應用在 Windows 憑證儲存區中可以存取指定的憑證,並且位置取決於[「 定價層](overview-hosting-plans.md):

- **隔離**層 - 在[本地電腦\我的](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)。 
- 所有其他層 - 在當前[使用者\我的](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)中。

在 C# 代碼中,您可以通過憑證指紋存取證書。 下列程式碼會載入指紋為 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的憑證。

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

在 Java 代碼中,您可以使用「主題通用名稱」欄位(請參閱[公鑰證書](https://en.wikipedia.org/wiki/Public_key_certificate))從「Windows-MY」儲存存取證書。 以下代碼簡報如何載入私密金鑰憑證:

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

對於不支援或對 Windows 憑證儲存的功能不足的語言,請參閱[從檔案載入憑證](#load-certificate-from-file)。

## <a name="load-certificate-in-linux-apps"></a>在 Linux 應用程式中載入憑證

應用`WEBSITE_LOAD_CERTIFICATES`設置使指定的證書可供 Linux 託管應用(包括自訂容器應用)作為檔案訪問。 這些檔案在以下目錄下找到:

- 私密憑證`/var/ssl/private` `.p12` ─ (檔案)
- 公開憑證`/var/ssl/certs` `.der` ─(檔案)

證書檔名是證書指紋。 以下 C# 程式碼展示如何在 Linux 應用中載入公共憑證。

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

要查看如何從 Node.js、PHP、Python、Java 或 Ruby 中的檔案中載入 TLS/SSL 憑證,請參閱相應語言或 Web 平台的文件。

## <a name="load-certificate-from-file"></a>從檔案載入憑證

如果需要載入手動上傳的憑證檔,最好使用[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)上載證書。 應使敏感數據(如私有證書)遠離原始程式碼管理。

> [!NOTE]
> ASP.NET和 ASP.NET Windows 上的 Core 也必須造訪憑證存儲,即使您從檔案載入證書也是如此。 在 Windows .NET 套用中載入憑證檔,在<a target="_blank" href="https://shell.azure.com" >雲外殼</a>中使用以下指令載入目前的使用者設定檔:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 在代碼中使用證書的此方法使用應用服務中的 TLS 功能,該功能要求你的應用位於**基本**層或以上。

以下 C# 範例從應用程式中的相對路徑載入公共憑證:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

要查看如何從 Node.js、PHP、Python、Java 或 Ruby 中的檔案中載入 TLS/SSL 憑證,請參閱相應語言或 Web 平台的文件。

## <a name="more-resources"></a>其他資源

* [使用 Azure 應用服務中的 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
