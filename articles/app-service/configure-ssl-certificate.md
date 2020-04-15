---
title: 新增和管理 TLS/SSL 憑證
description: 在 Azure App Service 中建立免費憑證、匯入 App Service 憑證、匯入 Key Vault 憑證，或購買 App Service 憑證。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 4edf710e575bbb26fb0e247e59ff5c796f16226e
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810600"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>在 Azure App Service 中新增 TLS/SSL 憑證

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本文說明如何將私人憑證或公開憑證建立、上傳或匯入到 App Service。 

一旦將憑證新增至您的 App Service 應用程式或[函數應用程式](https://docs.microsoft.com/azure/azure-functions/)後，您就可以[使用它來保護自訂 DNS 名稱](configure-ssl-bindings.md)，或[用於應用程式程式碼中](configure-ssl-certificate-in-code.md)。

下表列出您在 App Service 中所擁有可新增憑證的選項：

|選項|描述|
|-|-|
| 建立免費 App Service 受控憑證 (預覽) | 如果您只需要保護 `www` [自訂網域](app-service-web-tutorial-custom-domain.md)或 App Service 中的任何非裸網域時，此為您可輕鬆使用的私人憑證。 |
| 購買 App Service 憑證 | 此為由 Azure 管理的私人憑證。 它具有自動化憑證管理的簡易性，並兼具更新和匯出選項的彈性。 |
| 從金鑰保存庫匯入憑證 | 如果您使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 管理您的 [PKCS12 憑證](https://wikipedia.org/wiki/PKCS_12)，這會很有用。 請參閱[私人憑證需求](#private-certificate-requirements)。 |
| 上傳私人憑證 | 如果您已經有協力廠商提供者的私人憑證，您可以將該憑證上傳。 請參閱[私人憑證需求](#private-certificate-requirements)。 |
| 上傳公開憑證 | 公開憑證無法用來保護自訂網域，但如果您需要公開憑證來存取遠端資源，則可將公開憑證載入程式碼中。 |

## <a name="prerequisites"></a>Prerequisites

若要遵循本操作說明指南：

- [建立 App Service 應用程式](/azure/app-service/)。
- 僅適用於免費憑證：透過 [CNAME 記錄](app-service-web-tutorial-custom-domain.md#map-a-cname-record)將子網域 (例如 `www.contoso.com`) 對應至 App Service。

## <a name="private-certificate-requirements"></a>私人憑證需求

> [!NOTE]
> Azure Web Apps **不**支援 AES256，且所有 pfx 檔案都應該使用 TripleDES 加密。

[免費 App Service 受控憑證](#create-a-free-certificate-preview)或 [App Service 憑證](#import-an-app-service-certificate)已經符合 App Service 的需求。 如果您選擇將私人憑證上傳或匯入至 App Service，您的憑證就必須符合下列需求：

* [以受密碼保護的 PFX 檔案形式](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)匯出
* 包含長度至少為 2048 位元的私密金鑰
* 包含憑證鏈結中的所有中繼憑證

若要保護 TLS 繫結中的自訂網域，則憑證有額外的需求：

* 包含伺服器驗證的[擴充金鑰使用方法](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) (OID = 1.3.6.1.5.5.7.3.1)
* 由受信任的憑證授權單位簽署

> [!NOTE]
> **橢圓曲線密碼編譯 (ECC) 憑證**可搭配 App Service 使用，但不在本文討論範圍內。 請洽詢您的憑證授權單位，了解建立 ECC 憑證的確切步驟。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>建立免費憑證 (預覽)

免費 App Service 受控憑證是現成的解決方案，可在 App Service 中保護您的自訂 DNS 名稱。 它是一個功能完整的 TLS/SSL 憑證，由 App Service 管理且會自動更新。 此免費憑證有下列限制：

- 不支援萬用字元憑證。
- 不支援裸網域。
- 不可匯出。
- 不支援 DNS A 記錄。

> [!NOTE]
> 免費憑證由 DigiCert 所發行。 針對某些最上層網域，您必須使用下列值建立 [CAA 網域記錄](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)，以明確允許 DigiCert 作為憑證簽發者：`0 issue digicert.com`。
> 

若要建立免費的 App Service 受控憑證：

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從應用程式的左側導覽中，選取 [TLS/SSL 設定]   > [私密金鑰憑證 (.pfx)]   > [匯入 App Service 受控憑證]  。

![在 App Service 中建立免費憑證](./media/configure-ssl-certificate/create-free-cert.png)

任何透過 CNAME 記錄正確對應至您應用程式的非裸網域，都會列在對話方塊中。 選取要為其建立免費憑證的自訂網域，然後選取 [建立]  。 您只能為每個支援的自訂網域建立一個憑證。

當作業完成時，您會在 [私密金鑰憑證]  清單中看到憑證。

![免費憑證建立完成](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此憑證保護自訂網域，您仍然需要建立憑證繫結。 請依照[建立繫結](configure-ssl-bindings.md#create-binding)中的步驟。
>

## <a name="import-an-app-service-certificate"></a>匯入 App Service 憑證

如果您從 Azure 購買 App Service 憑證，Azure 會管理下列工作：

- 處理向 GoDaddy 的購買流程。
- 執行憑證的網域驗證。
- 維護 [Azure Key Vault](../key-vault/key-vault-overview.md) 中的憑證。
- 管理憑證更新 (請參閱[更新憑證](#renew-certificate))。
- 自動將憑證與 App Service 應用程式中匯入的複本同步化。

若要購買 App Service 憑證，請前往[開始訂購憑證](#start-certificate-order)。

如果您已經有使用中的 App Service 憑證，您可以：

- [將憑證匯入至 App Service](#import-certificate-into-app-service)。
- [管理憑證](#manage-app-service-certificates)，例如將憑證更新、重設金鑰和匯出。

### <a name="start-certificate-order"></a>開始訂購憑證

在 <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service 憑證建立頁面</a>中開始訂購 App Service 憑證。

![開始購買 App Service 憑證](./media/configure-ssl-certificate/purchase-app-service-cert.png)

使用下表來協助您設定憑證。 完成後，按一下 [建立]  。

| 設定 | 描述 |
|-|-|
| 名稱 | App Service 憑證的易記名稱。 |
| 裸網域主機名稱 | 在此處指定根網域。 根網域和 `www` 子網域*皆*受到發行憑證的保護。 在發行的憑證中，[一般名稱] 欄位包含根網域，[主體別名] 欄位則包含 `www` 網域。 若只要保護所有子網域，請在這裡指定子網域的完整網域名稱 (例如 `mysubdomain.contoso.com`)。|
| 訂用帳戶 | 會包含憑證的訂用帳戶。 |
| 資源群組 | 會包含憑證的資源群組。 您可以使用新的資源群組，或為您的 App Service 應用程式選取相同的資源群組。 |
| 憑證 SKU | 決定要建立的憑證類型：標準憑證或[萬用字元憑證](https://wikipedia.org/wiki/Wildcard_certificate)。 |
| 法律條款 | 按一下以確認您同意法律條款。 憑證是從 GoDaddy 取得的。 |

### <a name="store-in-azure-key-vault"></a>儲存在 Azure Key Vault 中

憑證購買程序完成後，您必須先完成一些其他的步驟，才能開始使用此憑證。 

選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後按一下 [憑證設定]   > [步驟 1：  存放區]。

![設定 App Service 憑證的 Key Vault 儲存體](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 是一項 Azure 服務，可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和祕密。 這是 App Service 憑證的儲存體選擇。

在 [Key Vault 狀態]  頁面中，按一下 [Key Vault 存放庫]  來建立新的保存庫或選擇現有的保存庫。 如果您選擇建立新的保存庫，請使用下表來協助您設定保存庫並按一下 [建立]。 在與 App Service 應用程式相同的訂用帳戶和資源群組內建立新的 Key Vault。

| 設定 | 描述 |
|-|-|
| 名稱 | 包含英數字元和虛線的唯一名稱。 |
| 資源群組 | 建議選取相同的資源群組作為您的 App Service 憑證。 |
| Location | 選取與 App Service 應用程式相同的位置。 |
| 定價層 | 如需詳細資訊，請參閱 [Azure Key Vault 定價詳細資料](https://azure.microsoft.com/pricing/details/key-vault/)。 |
| 存取原則| 定義應用程式以及允許的保存庫資源存取權。 您可以稍後設定它，並遵循[將金鑰保存庫的存取權授與數個應用程式](../key-vault/key-vault-group-permissions-for-apps.md)中的步驟。 |
| 虛擬網路存取 | 限制某些 Azure 虛擬網路的保存庫存取權。 您可以稍後設定它，並遵循[設定 Azure Key Vault 防火牆和虛擬網路](../key-vault/key-vault-network-security.md)中的步驟。 |

選取保存庫之後，關閉 [Key Vault 存放庫]  頁面。 [步驟 1：  儲存] 選項應該會顯示綠色核取記號來表示已成功。 讓頁面保持開啟，以供下一個步驟使用。

### <a name="verify-domain-ownership"></a>確認網域擁有權

從您在上一個步驟中使用的相同 [憑證設定]  頁面，按一下 [步驟 2：  驗證]。

![驗證 App Service 憑證的網域](./media/configure-ssl-certificate/verify-domain.png)

選取 [App Service 驗證]  。 您已經將網域對應至您的 Web 應用程式 (請參閱[必要條件](#prerequisites))，因此它已經過驗證。 只要按一下 [驗證]  即可完成此步驟。 按一下 [重新整理]  按鈕，直到「憑證已經過網域驗證」  訊息出現為止。

> [!NOTE]
> 支援的網域驗證方法有四種： 
> 
> - **App Service** - 當網域已經對應至相同訂用帳戶中的 App Service 應用程式時最方便的選項。 它使用 App Service 應用程式在實質上已通過網域擁有權驗證的這個優勢。
> - **網域** - 驗證[您購自 Azure 的 App Service 網域](manage-custom-dns-buy-domain.md)。 Azure 會自動為您新增驗證 TXT 記錄並完成程序。
> - **郵件** - 將電子郵件傳送給網域管理員來驗證網域。 當您選取此選項時，系統會提供指示。
> - **手動** - 使用 HTML 網頁 (僅限**標準**憑證) 或 DNS TXT 記錄驗證網域。 當您選取此選項時，系統會提供指示。

### <a name="import-certificate-into-app-service"></a>將憑證匯入至 App Service

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從您應用程式的左側導覽中，選取 [TLS/SSL 設定]   >   [私密金鑰憑證 (.pfx)] > [匯入 App Service 憑證]  。

![在 App Service 中匯入 App Service 憑證](./media/configure-ssl-certificate/import-app-service-cert.png)

選取您剛剛購買的憑證，然後選取 [確定]  。

當作業完成時，您會在 [私密金鑰憑證]  清單中看到憑證。

![App Service 憑證匯入完成](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此憑證保護自訂網域，您仍然需要建立憑證繫結。 請依照[建立繫結](configure-ssl-bindings.md#create-binding)中的步驟。
>

## <a name="import-a-certificate-from-key-vault"></a>從金鑰保存庫匯入憑證

如果您使用 Azure Key Vault 管理憑證，只要 PKCS12 憑證[符合需求](#private-certificate-requirements)，便可將該憑證從 Key Vault 匯入到 App Service。

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從您應用程式的左側導覽中，選取 [TLS/SSL 設定]   >   [私密金鑰憑證 (.pfx)] > [匯入 Key Vault 憑證]  。

![在 App Service 中匯入 Key Vault 憑證](./media/configure-ssl-certificate/import-key-vault-cert.png)

使用下表來協助您選取憑證。

| 設定 | 描述 |
|-|-|
| 訂用帳戶 | Key Vault 所屬的訂用帳戶。 |
| Key Vault | 具有所要匯入憑證的保存庫。 |
| 憑證 | 從保存庫內的 PKCS12 憑證清單中選取。 保存庫中所有的 PKCS12 憑證會連同其指紋一併列出，但在 App Service 中並非皆受到支援。 |

當作業完成時，您會在 [私密金鑰憑證]  清單中看到憑證。 如果匯入失敗並發生錯誤，則表示憑證不符合 [App Service 的需求](#private-certificate-requirements)。

![Key Vault 憑證匯入完成](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此憑證保護自訂網域，您仍然需要建立憑證繫結。 請依照[建立繫結](configure-ssl-bindings.md#create-binding)中的步驟。
>

## <a name="upload-a-private-certificate"></a>上傳私人憑證

從憑證提供者取得憑證後，請遵循本節中的步驟，讓憑證可用於 App Service。

### <a name="merge-intermediate-certificates"></a>合併中繼憑證

如果憑證授權單位在憑證鏈結中提供多個憑證，您需要依序合併憑證。

若要這樣做，請在文字編輯器中開啟您收到的每個憑證。

為合併的憑證建立一個檔案，並取名為 _mergedcertificate.crt_。 在文字編輯器中，將每個憑證的內容複製到這個檔案中。 憑證的順序應該遵循在憑證鏈結中的順序，開頭為您的憑證，以及結尾為根憑證。 看起來會像下列範例：

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>將憑證匯出為 PFX

使用與憑證要求共同產生的私密金鑰，將您合併的 TLS/SSL 憑證匯出。

如果您是使用 OpenSSL 產生憑證要求，則已建立私密金鑰檔案。 若要將您的憑證匯出為 PFX，請執行下列命令。 將預留位置 _&lt;private-key-file>_ 和 _&lt;merged-certificate-file>_ 以您的私密金鑰與合併的憑證檔案的路徑取代。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

請在出現提示時定義一個匯出密碼。 您之後將 TLS/SSL 憑證上傳至 App Service 時，將會用到這組密碼。

如果您使用 IIS 或 _Certreq.exe_ 產生憑證要求，請將憑證安裝至本機電腦，然後[將憑證匯出為 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

### <a name="upload-certificate-to-app-service"></a>上傳憑證至 App Service

您現在已準備好將憑證上傳至 App Service。

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從您應用程式的左側導覽中，選取 [TLS/SSL 設定]   >   [私密金鑰憑證 (.pfx)] > [上傳憑證]  。

![在 App Service 中上傳私人憑證](./media/configure-ssl-certificate/upload-private-cert.png)

在 [PFX 憑證檔案]  中，選取您的 PFX 檔案。 在 [憑證密碼]  中，輸入您將 PFX 檔案匯出時所建立的密碼。 完成後，請按一下 [上傳]  。 

當作業完成時，您會在 [私密金鑰憑證]  清單中看到憑證。

![憑證上傳完成](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> 若要使用此憑證保護自訂網域，您仍然需要建立憑證繫結。 請依照[建立繫結](configure-ssl-bindings.md#create-binding)中的步驟。
>

## <a name="upload-a-public-certificate"></a>上傳公開憑證

公開憑證支援 *.cer* 格式。 

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從您應用程式的左側導覽中，按一下 [TLS/SSL 設定]   >   [私密金鑰憑證 (.pfx)] > [上傳公開金鑰憑證]  。

在 [名稱]  中，輸入憑證的名稱。 在 [CER 憑證檔案]  中，選取您的 CER 檔案。

按一下 [上傳]  。

![在 App Service 中上傳公開憑證](./media/configure-ssl-certificate/upload-public-cert.png)

憑證一旦上傳後，請複製憑證指紋，並請參閱[讓憑證可供存取](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)。

## <a name="manage-app-service-certificates"></a>管理 App Service 憑證

本節說明如何管理您在 [匯入 App Service 憑證](#import-an-app-service-certificate)中購買的 App Service 憑證。

- [重設憑證的金鑰](#rekey-certificate)
- [更新憑證](#renew-certificate)
- [匯出憑證](#export-certificate)
- [刪除憑證](#delete-certificate)

### <a name="rekey-certificate"></a>重設憑證的金鑰

如果您認為您憑證的私密金鑰遭到入侵，您可以重設憑證的金鑰。 選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後從左側導覽中選取 [重設金鑰和同步處理]  。

按一下 [重設金鑰]  來啟動處理程序。 此程序需要 1 - 10 分鐘才能完成。

![重設 App Service 憑證的金鑰](./media/configure-ssl-certificate/rekey-app-service-cert.png)

重設憑證的金鑰，會以憑證授權單位發行的新憑證變更憑證。

重設金鑰作業完成後，請按一下 [同步]  。同步作業會自動更新 App Service 中憑證的主機名稱繫結，而不會對您的應用程式造成任何停機。

> [!NOTE]
> 如果您未按一下 [同步]  ，則 App Service 會在 48 小時內自動同步處理您的憑證。

### <a name="renew-certificate"></a>更新憑證

若要隨時開啟憑證的自動更新，請選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後按一下左側導覽中的 [自動更新設定]  。 根據預設，App Service 憑證的有效期間為一年。

選取 [開啟]  ，然後按一下 [儲存]  。 如果您已經開啟自動更新，憑證可以在過期前的 60 天開始自動更新。

![自動更新 App Service 憑證](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

若要改為手動更新憑證，請按一下 [手動更新]  。 您可以在過期前的 60 天要求手動更新憑證。

更新作業完成後，請按一下 [同步]  。同步作業會自動更新 App Service 中憑證的主機名稱繫結，而不會對您的應用程式造成任何停機。

> [!NOTE]
> 如果您未按一下 [同步]  ，則 App Service 會在 48 小時內自動同步處理您的憑證。

### <a name="export-certificate"></a>匯出憑證

由於 App Service 憑證是 [Key Vault 秘密](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)，因此您可以匯出其 PFX 複本，並將它用於其他 Azure 服務或 Azure 外部。

若要將 App Service 憑證匯出為 PFX 檔案，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令。 如果您已[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)，則也可以在本機執行。 以您在[建立 App Service 憑證](#start-certificate-order)時使用的名稱取代預留位置。

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

下載的 *appservicecertificate.pfx* 檔案是原始的 PKCS12 檔案，其中同時包含公開憑證和私人憑證。 每當系統提示您時，匯入密碼和 PEM 複雜密碼都使用空字串。

### <a name="delete-certificate"></a>Delete certificate 

刪除 App Service 憑證是不可更改且無法復原的。 刪除 App Service 憑證資源會導致憑證遭到撤銷。 App Service 中任何與此憑證的繫結都會變成無效。 為了防止意外刪除，Azure 會鎖定憑證。 若要刪除 App Service 憑證，您必須先移除憑證的刪除鎖定。

選取 [App Service 憑證](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders)頁面中的憑證，然後在左側導覽中選取 [鎖定]  。

在您的憑證上尋找鎖定類型為 [刪除]  的鎖定。 選取其右側的 [刪除]  。

![App Service 憑證的刪除鎖定](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

現在您可以刪除 App Service 憑證。 從左側導覽中，選取 [總覽]   > [刪除]  。 在確認對話方塊中，輸入憑證名稱，然後選取 [確定]  。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>其他資源

* [在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
* [強制使用 HTTPS](configure-ssl-bindings.md#enforce-https)
* [強制使用 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [在 Azure App Service 的程式碼中使用 TLS/SSL 憑證](configure-ssl-certificate-in-code.md)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
