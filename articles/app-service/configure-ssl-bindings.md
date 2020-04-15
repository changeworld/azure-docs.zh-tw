---
title: 使用 TLS/SSL 繫結保護自訂 DNS
description: 藉由建立具有憑證的 TLS/SSL 繫結，保護對自訂網域的 HTTPS 存取。 藉由強制執行 HTTPS 或 TLS 1.2 改善網站的安全性。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 9792181379bfa6f9e0337bf14208fe853c16b745
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811742"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱

本文說明如何藉由建立憑證繫結，在您的 [App Service 應用程式](https://docs.microsoft.com/azure/app-service/)或[函式應用程式](https://docs.microsoft.com/azure/azure-functions/)中保護[自訂網域](app-service-web-tutorial-custom-domain.md)。 完成此作業後，您將可在自訂 DNS 名稱的 `https://` 端點存取您的 App Service 應用程式 (例如 `https://www.contoso.com`)。 

![Web 應用程式與自訂 TLS/SSL 憑證](./media/configure-ssl-bindings/app-with-custom-ssl.png)

要使用憑證保護[自訂網域](app-service-web-tutorial-custom-domain.md)，必須執行兩個步驟：

- [將私人憑證新增至 App Service](configure-ssl-certificate.md) (其符合所有[私人憑證需求](configure-ssl-certificate.md#private-certificate-requirements)。
-  建立對應自訂網域的 TLS 繫結。 本文說明其中的第二個步驟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 升級應用程式的定價層
> * 使用憑證保護自訂網域
> * 強制使用 HTTPS
> * 強制使用 TLS 1.1/1.2
> * 使用指令碼將 TLS 管理自動化

## <a name="prerequisites"></a>Prerequisites

若要遵循本操作說明指南：

- [建立 App Service 應用程式](/azure/app-service/)
- [將網域名稱對應至您的應用程式](app-service-web-tutorial-custom-domain.md)或[在 Azure 中購買並設定它](manage-custom-dns-buy-domain.md)
- [將私人憑證新增至您的應用程式](configure-ssl-certificate.md)

> [!NOTE]
> 要新增私人憑證，最簡單的方式是[建立免費的 App Service 受控憑證](configure-ssl-certificate.md#create-a-free-certificate-preview) (預覽)。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>保護自訂網域

請執行下列步驟：

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的左側功能表中，選取 [應用程式服務]   >  **\<app-name>** 。

從應用程式的左側導覽中，透過下列方式啟動 [TLS/SSL 繫結]  對話方塊：

- 選取 [自訂網域]   > [新增繫結] 
- 選取 [TLS/SSL 設定]   > [新增 TLS/SSL 繫結] 

![新增繫結至網域](./media/configure-ssl-bindings/secure-domain-launch.png)

在 [自訂網域]  中，選取要新增繫結的自訂網域。

如果您的應用程式已經有所選自訂網域的憑證，請移至 [直接建立系結](#create-binding)。 否則，請繼續進行。

### <a name="add-a-certificate-for-custom-domain"></a>新增自訂網域的憑證

如果您的應用程式沒有所選自訂網域的憑證，則您有兩個選項：

- **上傳 PFX 憑證** - 遵循[上傳私人憑證](configure-ssl-certificate.md#upload-a-private-certificate)中的工作流程，然後在這裡選取此選項。
- **匯入 App Service 憑證** - 遵循[匯入 App Service 憑證](configure-ssl-certificate.md#import-an-app-service-certificate)中的工作流程，然後在這裡選取此選項。

> [!NOTE]
> 您也可以[建立免費憑證](configure-ssl-certificate.md#create-a-free-certificate-preview) (預覽) 或[匯入 Key Vault 憑證](configure-ssl-certificate.md#import-a-certificate-from-key-vault)，但您必須個別加以執行，然後返回 [TLS/SSL 繫結]  對話方塊。

### <a name="create-binding"></a>建立繫結

利用下表在 [TLS/SSL 繫結]  對話方塊中設定 TLS 繫結，然後按一下 [新增繫結]  。

| 設定 | 描述 |
|-|-|
| 自訂網域 | 要新增 TLS/SSL 繫結的網域名稱。 |
| 私人憑證指紋 | 要繫結的憑證。 |
| TLS/SSL 類型 | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - 可新增多個 SNI SSL 繫結。 此選項可允許多個 TLS/SSL 憑證保護同一個 IP 位址上的多個網域。 現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI (如需詳細資訊，請參閱[伺服器名稱指示](https://wikipedia.org/wiki/Server_Name_Indication))。</li><li>**IP SSL** - 只能新增一個 IP SSL 繫結。 此選項只允許一個 TLS/SSL 憑證保護專用的公用 IP 位址。 設定繫結之後，請依照[為 IP SSL 重新對應 A 記錄](#remap-a-record-for-ip-ssl)中的步驟執行。<br/>僅在生產或隔離層中支援 IP SSL。 </li></ul> |

作業完成後，自訂網域的 TLS/SSL 狀態會變更為**安全**。

![TLS/SSL 繫結成功](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **自訂網域**中的**安全**狀態表示網域已透過憑證來保護，但是 App Service 不會檢查憑證是否已自我簽署或已過期等等，而這些狀況會導致瀏覽器顯示錯誤或警告。

## <a name="remap-a-record-for-ip-ssl"></a>將 IP SSL 的 A 記錄重新對應

如果您未在應用程式中使用 IP SSL，請跳至[測試自訂網域的 HTTPS](#test-https)。

根據預設，您的應用程式會使用共用的公用 IP 位址。 當您將憑證與 IP SSL 繫結時，App Service 會為您的應用程式建立新的專用 IP 位址。

如果您已將 A 記錄對應至應用程式，請使用這個新的專用 IP 位址來更新網域登錄。

應用程式的 [自訂網域]  頁面即會使用新的專用 IP 位址加以更新。 [複製此 IP 位址](app-service-web-tutorial-custom-domain.md#info)，然後[將 A 記錄重新對應](app-service-web-tutorial-custom-domain.md#map-an-a-record)到這個新的 IP 位址。

## <a name="test-https"></a>測試 HTTPS

在各種瀏覽器中瀏覽至 `https://<your.custom.domain>`，以確認它是否為您的應用程式提供服務。

![入口網站瀏覽至 Azure 應用程式](./media/configure-ssl-bindings/app-with-custom-ssl.png)

您的應用程式程式碼可以透過 "x-appservice-proto" 標頭來檢查通訊協定。 標頭的值會是 `http` 或 `https`。 

> [!NOTE]
> 如果您的應用程式出現憑證驗證錯誤，您可能使用了自我簽署憑證。
>
> 如果不是，在您將憑證匯出為 PFX 檔案時，可能遺漏了中繼憑證。

## <a name="prevent-ip-changes"></a>防止 IP 變更

當您刪除繫結時，您可以變更輸入 IP 位址，即使該繫結是 IP SSL 亦然。 當您更新已在 IP SSL 繫結中的憑證時，這一點尤為重要。 若要避免變更應用程式的 IP 位址，請依序執行下列步驟：

1. 上傳新憑證。
2. 將新的憑證繫結至您要的自訂網域，而不刪除舊憑證。 此動作會取代繫結，而不會移除舊的繫結。
3. 刪除舊憑證。 

## <a name="enforce-https"></a>強制使用 HTTPS

根據預設，所有人都仍能使用 HTTP 來存取您的應用程式。 您可以將所有 HTTP 要求重新都導向至 HTTPS 連接埠。

在應用程式頁面的左側導覽中，選取 [SSL 設定]  。 然後，在 [僅限 HTTPS]  中選取 [開啟]  。

![強制使用 HTTPS](./media/configure-ssl-bindings/enforce-https.png)

當作業完成時，瀏覽至指向您的應用程式的任何 HTTP URL。 例如：

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>強制使用 TLS 版本

根據預設，您的應用程式會允許 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2，此為業界標準 (例如 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)) 建議的 TLS 層級。 若要強制使用不同的 TLS 版本，請遵循下列步驟：

在應用程式頁面的左側導覽中，選取 [SSL 設定]  。 然後，在 [TLS 版本]  中，選取您想要的最低 TLS 版本。 此設定只會控制內送的呼叫。 

![強制使用 TLS 1.1 或 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

當作業完成時，您的應用程式會拒絕與較低 TLS 版本的所有連線。

## <a name="handle-tls-termination"></a>處理 TLS 終止

在 App Service 中，[TLS 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

語言專屬的設定指南 (如 [Linux Node.js 設定](containers/configure-language-nodejs.md#detect-https-session)指南) 會說明如何在應用程式的程式碼中偵測 HTTPS 工作階段。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>其他資源

* [在 Azure App Service 的程式碼中使用 TLS/SSL 憑證](configure-ssl-certificate-in-code.md)
* [常見問題集：App Service 憑證](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
