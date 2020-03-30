---
title: 在代碼中使用 SSL 憑證
description: 瞭解如何在代碼中使用用戶端憑證。 使用用戶端憑證對遠端資源進行身份驗證，或使用它們運行加密任務。
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671885"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>在 Azure 應用服務中的代碼中使用 SSL 憑證

在應用程式代碼中，可以訪問[添加到應用服務 的公共或私有證書](configure-ssl-certificate.md)。 你的應用代碼可能充當用戶端並訪問需要證書身份驗證的外部服務，或者可能需要執行加密任務。 本說明指南演示如何在應用程式代碼中使用公共或私有證書。

在代碼中使用證書的此方法使用應用服務中的 SSL 功能，該功能要求你的應用位於**基本**層或以上。 如果你的應用處于 **"免費**"或"**共用**"層，則可以[在應用存儲庫中包括證書檔](#load-certificate-from-file)。

當您讓 App Service 管理您的 SSL 憑證時，您可以分開維護憑證以及應用程式程式碼，並保護您的敏感性資料。

## <a name="prerequisites"></a>Prerequisites

若要遵循本操作說明指南：

- [創建應用服務應用](/azure/app-service/)
- [向應用添加證書](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>查找指紋

在<a href="https://portal.azure.com" target="_blank">Azure 門戶</a>中，從左側功能表中選擇**應用服務** > **\<應用名稱>**。

從應用的左側導航中，選擇**TLS/SSL 設置**，然後選擇**私密金鑰證書 （.pfx）** 或**公開金鑰證書 （.cer）。**

查找要使用的證書並複製指紋。

![複製證書指紋](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>使證書可訪問

要訪問應用代碼中的證書，請通過在<a target="_blank" href="https://shell.azure.com" >雲外殼</a>中運行以下命令`WEBSITE_LOAD_CERTIFICATES`，將其指紋添加到應用設置中：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

要使所有證書都訪問，請將 該`*`值設置為 。

## <a name="load-certificate-in-windows-apps"></a>在 Windows 應用中載入證書

應用`WEBSITE_LOAD_CERTIFICATES`設置使 Windows 託管應用在 Windows 憑證存放區區中可以訪問指定的證書，並且位置取決於[定價層](overview-hosting-plans.md)：

- **隔離**層 - 在[本地電腦\我的](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)。 
- 所有其他層 - 在當前[使用者\我的](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)中。

在 C# 代碼中，您可以通過證書指紋訪問證書。 下列程式碼會載入指紋為 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的憑證。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

在 JAVA 代碼中，您可以使用"主題通用名稱"欄位（請參閱[公開金鑰證書](https://en.wikipedia.org/wiki/Public_key_certificate)）從"Windows-MY"存儲訪問證書。 以下代碼演示如何載入私密金鑰證書：

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

對於不支援或對 Windows 憑證存放區的支援不足的語言，請參閱[從檔載入證書](#load-certificate-from-file)。

## <a name="load-certificate-in-linux-apps"></a>在 Linux 應用程式中載入證書

應用`WEBSITE_LOAD_CERTIFICATES`設置使指定的證書可供 Linux 託管應用（包括自訂容器應用）作為檔訪問。 這些檔在以下目錄下找到：

- 私人證書`/var/ssl/private`- `.p12` （檔）
- 公共證書`/var/ssl/certs`- `.der` （檔）

證書檔案名是證書指紋。 以下 C# 代碼演示如何在 Linux 應用中載入公共證書。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

要查看如何在 Node.js、PHP、Python、JAVA 或 Ruby 中從檔中載入 SSL 憑證，請參閱相應語言或 Web 平臺的文檔。

## <a name="load-certificate-from-file"></a>從檔載入證書

如果需要載入手動上傳的證書檔，最好使用[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)上載證書。 應使敏感性資料（如私有證書）遠離原始程式碼管理。

> [!NOTE]
> ASP.NET和 ASP.NET Windows 上的 Core 也必須訪問憑證存放區，即使您從檔載入證書也是如此。 要在 Windows .NET 應用中載入證書檔，在<a target="_blank" href="https://shell.azure.com" >雲外殼</a>中使用以下命令載入當前使用者設定檔：
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 在代碼中使用證書的此方法使用應用服務中的 SSL 功能，該功能要求你的應用位於**基本**層或以上。

以下 C# 示例從應用中的相對路徑載入公共證書：

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

要查看如何在 Node.js、PHP、Python、JAVA 或 Ruby 中從檔中載入 SSL 憑證，請參閱相應語言或 Web 平臺的文檔。

## <a name="more-resources"></a>其他資源

* [使用 SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
