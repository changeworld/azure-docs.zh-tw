---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Maverics Identity Orchestrator SAML Connector 整合 | Microsoft Docs
description: 了解如何在 Azure Active Directory 及 Maverics Identity Orchestrator SAML Connector 間設定單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: a4bfe2b87f3f2242189a78d9a31a89d82720fd37
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862049"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>教學課程：整合 Azure AD 單一登入與 Maverics Identity Orchestrator SAML Connector

Strata 提供簡單的方法來整合內部部署應用程式與 Azure Active Directory (Azure AD) 以進行驗證和存取控制。

本文逐步解說如何設定 Maverics Identity Orchestrator 以達到以下目標：
* 在登入舊版內部部署應用程式期間，以逐步增加的方式將使用者從內部部署身分識別系統遷移到 Azure AD。
* 將來自舊版 Web 存取管理產品 (例如 CA SiteMinder 或 Oracle Access Manager) 的登入要求路由傳送至 Azure AD。
* 向 Azure AD 驗證使用者之後，驗證使用 HTTP 標頭或專屬工作階段 Cookie 保護的內部部署應用程式使用者。

Strata 提供可讓您在內部部署或雲端中部署的軟體， 協助您探索、連線及協調識別提供者，同時建立混合式和多重雲端企業的分散式身分識別管理。

本教學課程將示範如何遷移目前由舊版 Web 存取管理產品 (CA SiteMinder) 所保護的內部部署 Web 應用程式，以使用 Azure AD 進行驗證和存取控制。 基礎步驟如下：
1. 安裝 Maverics Identity Orchestrator。
2. 向 Azure AD 註冊您的企業應用程式，並將其設定為使用 SAML 型單一登入 (SSO) 的 Maverics Azure AD SAML Zero Code Connector。
3. 整合 Maverics 與 SiteMinder 和輕量型目錄存取協定 (LDAP) 使用者存放區。
4. 設定 Azure Key Vault，並將 Maverics 設定為其祕密管理提供者。
5. 使用 Maverics 來示範使用者遷移和工作階段抽象，以提供內部部署 JAVA Web 應用程式的存取權。

如需其他安裝和組態指示，請移至 [Strata 網站](https://www.strata.io)。

## <a name="prerequisites"></a>先決條件

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
- 已啟用 Maverics Identity Orchestrator SAML Connector SSO 型訂用帳戶。 若要取得 Maverics 軟體，請連絡 [Strata 業務人員](mailto:sales@strata.io)。

## <a name="install-maverics-identity-orchestrator"></a>安裝 Maverics Identity Orchestrator

若要開始使用 Maverics Identity Orchestrator 安裝，請參閱[安裝指示](https://www.strata.io)。

### <a name="system-requirements"></a>系統需求
* 支援的作業系統
  * RHEL 7+
  * CentOS 7+

* 相依性
  * systemd

### <a name="installation"></a>安裝

1. 取得最新的 Maverics Redhat Package Manager (RPM) 套件。 將套件複製到要安裝 Maverics 軟體的系統。

2. 安裝 Maverics 套件，並以您的檔案名稱取代 `maverics.rpm`。

    `sudo rpm -Uvf maverics.rpm`

3. 安裝 Maverics 後，其會以服務的形式在 `systemd` 下執行。 若要確認服務正在執行，請執行下列命令：

    `sudo systemctl status maverics`

依預設，Maverics 會安裝在 /usr/local/bin 目錄中。

安裝 Maverics 之後，會在 /etc/maverics 目錄中建立預設的 maverics.yaml 檔案。 在您編輯設定以包含 `workflows` 和 `connectors` 之前，組態檔看起來會像這樣：

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>設定選項
### <a name="version"></a>版本
`version` 欄位會宣告所使用的組態檔版本。 如果未指定版本，則會使用最新的組態版本。

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` 會宣告 Orchestrator 將接聽的位址。 如果位址的主機區段為空白，Orchestrator 將會接聽本機系統所有可用的單點傳播和任意傳播 IP 位址。 如果位址的連接埠區段為空白，則會自動選擇連接埠號碼。

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` 欄位會宣告傳輸層安全性 (TLS) 物件的對應。 TLS 物件可以由連接器和 Orchestrator 伺服器使用。 如需所有可用的 TLS 選項，請參閱 `transport` 的套件文件。

當您使用 SAML 型 SSO 時，Microsoft Azure 需要透過 TLS 進行通訊。 如需有關產生憑證的詳細資訊，請移至 [Let's Encrypt 網站](https://letsencrypt.org/getting-started/)。

`maverics` 金鑰會保留給 Orchestrator 伺服器。 所有其他金鑰都可供使用，而且可以用來將 TLS 物件插入指定的連接器中。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Include 檔案

您可以在其個別的組態檔中定義 `connectors` 和 `workflows`，並在 maverics.yaml 檔案中使用 `includeFiles` 加以參考，如以下範例所示：

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

本教學課程使用單一 maverics.yaml 組態檔。

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>使用 Azure Key Vault 作為祕密提供者

### <a name="manage-secrets"></a>管理祕密

Maverics 可以與各種祕密管理解決方案整合，以便載入密碼。 目前的整合項目包括檔案、Hashicorp 保存庫和 Azure Key Vault。 如果未指定祕密管理解決方案，Maverics 會預設為以純文字載入 maverics.yaml 檔案中的祕密。

若要將值宣告為 maverics.yaml 組態檔中的祕密，請使用角括弧包裝祕密：

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>從檔案載入祕密

1. 若要從檔案載入祕密，請使用下列方式，在 /etc/maverics/maverics.env 檔案 中新增環境變數 `MAVERICS_SECRET_PROVIDER`：

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. 執行此項目以重新啟動 Maverics 服務：

   `sudo systemctl restart maverics`

secrets.yaml 檔案內容可以填入任何 `secrets` 數字。

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>設定 Azure Key Vault

您可以使用 Azure 入口網站或 Azure CLI 來設定 Azure Key Vault。

**使用 Azure 入口網站**
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. [建立新金鑰保存庫](../../key-vault/general/quick-create-portal.md)。
1. [將祕密新增至金鑰保存庫](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。
1. [向 Azure AD 註冊應用程式](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。
1. [授權應用程式使用祕密](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。

**使用 Azure CLI**

1. 開啟 [Azure CLI](/cli/azure/install-azure-cli)，接著輸入下列命令：

    ```shell
    az login
    ```

1. 執行下列命令來建立新的金鑰保存庫：
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. 執行下列命令，將祕密新增到金鑰保存庫：
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. 執行下列命令，向 Azure AD 註冊應用程式：
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. 執行下列命令，授權應用程式使用祕密：
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. 若要從 Azure Key Vault 載入祕密，請使用 azure-credentials.json 檔案中找到的認證，使用下列模式在 /etc/maverics/maverics.env 檔案中設定環境變數 `MAVERICS_SECRET_PROVIDER`：
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. 重新啟動 Maverics 服務：`sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>在適用於 SAML 型 SSO 的 Azure AD 中設定您的應用程式

1. 在您的 Azure AD 租用戶中，移至 **企業應用程式**，搜尋 **Maverics Identity Orchestrator SAML Connector** 然後選取。

1. 在 Maverics Identity Orchestrator SAML Connector 的 **屬性** 窗格中，將 **是否需要指派使用者？** 設定為 **否**，讓新遷移的使用者可以使用應用程式。

1. 在 Maverics Identity Orchestrator SAML Connector 的 **概觀** 窗格中，選取 [設定單一登入]，然後選取 [SAML]。

1. 在 Maverics Identity Orchestrator SAML Connector 的 **SAML 型登入** 窗格中，選取 [編輯] (鉛筆圖示) 按鈕編輯 **基本 SAML 組態**。

   ![「基本 SAML 組態」編輯按鈕的螢幕擷取畫面。](common/edit-urls.png)

1. 以下列格式的 URL 輸入 **實體識別碼**：`https://<SUBDOMAIN>.maverics.org`。 實體識別碼必須是租用戶中所有應用程式的唯一名稱。 儲存此處輸入的值，以納入 Maverics 組態。

1. 以下列格式輸入 **回復 URL**︰`https://<AZURECOMPANY.COM>/<MY_APP>/`。 

1. 以下列格式輸入 **登入 URL**︰`https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`。 

1. 選取 [儲存]。

1. 在 **SAML 簽署憑證** 區段中，選取 [複製] 按鈕以複製 **應用程式同盟中繼資料 URL**，並將資料儲存在您的電腦上。

    ![「SAML 簽署憑證」複製按鈕的螢幕擷取畫面。](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>設定 Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector 支援 OpenID Connect 和 SAML Connect。 若要設定連接器，請執行下列動作： 

1. 若要啟用 SAML 型 SSO，請設定 `authType: saml`。

1. 以下列格式建立 `samlMetadataURL` 的值：`samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`。

1. 定義使用者使用 Azure 認證登入之後，Azure 會將使用者重新導向回的應用程式 URL。 請使用下列格式：`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`。

1. 複製上述設定的 EntityID 值：`samlEntityID: https://<SUBDOMAIN>.maverics.org`。

1. 從 Azure AD 會用來在 SAML 回應後使用的「回復 URL」中複製值：`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`。

1. 使用 [OpenSSL 工具](https://www.openssl.org/source/)，產生用來保護 Maverics Identity Orchestrator 工作階段資訊的 JSON Web 權杖 (JWT) 簽署金鑰：

    ```shell 
    openssl rand 64 | base64
    ```
1. 將回應複製到 `jwtSigningKey` 組態屬性：`jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`。

## <a name="attributes-and-attribute-mapping"></a>屬性和屬性對應
屬性對應是用來定義設定使用者之後，從來源內部部署使用者目錄到 Azure AD 租用戶的使用者屬性對應。

屬性會決定可能會在宣告中傳回給應用程式、傳入工作階段 Cookie，或在 HTTP 標頭變數中傳遞至應用程式的使用者資料。

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>設定 Maverics Identity Orchestrator Azure AD SAML Connector YAML 檔案

您的 Maverics Identity Orchestrator Azure AD Connector configuration 看起來會類似：

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>將使用者遷移至 Azure AD 租用戶

請遵循此組態，從 Web 存取管理產品 (例如 CA SiteMinder、Oracle Access Manager 或 IBM Tivoli) 以漸進方式遷移使用者。 您也可以從輕量型目錄存取協定 (LDAP) 目錄或 SQL 資料庫進行遷移。

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>在 Azure AD 中設定您的應用程式權限以建立使用者

1. 在您的 Azure AD 租用戶中，移至 `App registrations` 並選取 [Maverics Identity Orchestrator SAML Connector] 應用程式。

1. 在 **Maverics Identity Orchestrator SAML Connector | 憑證和祕密** 窗格中，選取 `New client secret` 然後選取到期選項。 選取 [複製] 按鈕以複製祕密，並儲存在您的電腦上。

1. 在 [Maverics Identity Orchestrator SAML Connector | API 權限] 窗格中，選取 [新增權限]，然後在 **要求 API 權限** 窗格中選取 [Microsoft Graph] 和 [應用程式權限]。 

1. 在下一個畫面上，選取 [User.ReadWrite.All]，然後選取 [新增權限]。 

1. 回到 **API 權限** 窗格中，選取 [授與管理員同意]。

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>設定 Maverics Identity Orchestrator SAML Connector YAML 檔案以移轉使用者

若要啟用使用者移轉工作流程，請將此額外屬性新增至組態檔：
1. 以下列格式輸入 **Azure Graph URL**︰`graphURL: https://graph.microsoft.com`。
1. 以下列格式輸入 **OAuth Token URL**︰`oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`。
1. 以下列格式輸入先前產生的用戶端密碼：`oauthClientSecret: <CLIENT SECRET>`。


您的最終 Maverics Identity Orchestrator Azure AD Connector 組態檔看起來會類似：

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>為 SiteMinder 設定 Maverics Zero Code Connector

您可以使用 SiteMinder 連接器，將使用者遷移至 Azure AD 租用戶。 您可以使用新建立的 Azure AD 身分識別和認證，將使用者登入受 SiteMinder 保護的舊版內部部署應用程式。

在本教學課程中，已將 SiteMinder 設定為使用表單型驗證和 `SMSESSION` Cookie 來保護繼承應用程式。 若要與透過 HTTP 標頭使用驗證和工作階段資訊的應用程式整合，您必須將標頭模擬組態新增至連接器。

這個範例會將 `username` 屬性對應至 `SM_USER` HTTP 標頭：

```yaml
  headers:
    SM_USER: username
```

將 `proxyPass` 設定為要求的代理位置。 通常此位置會是受保護應用程式的主機。

當您將使用者重新導向以進行驗證時，`loginPage` 應該符合 SiteMinder 目前使用之登入表單的 URL。

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>為 LDAP 設定 Maverics Zero Code Connector

當應用程式受到 Web 存取管理 (WAM) 產品 (例如 SiteMinder) 的保護時，使用者身分識別和屬性通常會儲存在 LDAP 目錄中。

此連接器組態會示範如何連線到 LDAP 目錄。 連接器會設定 SiteMinder 的使用者存放區，以便在移轉工作流程期間收集正確的使用者設定檔資訊，並可在 Azure AD 中建立對應的使用者。

* `baseDN` 指定目錄中要對其執行 LDAP 搜尋的位置。

* `url` 是要連線之 LDAP 伺服器的位址和連接埠。

* `serviceAccountUsername` 是用來連線到 LDAP 伺服器的使用者名稱，通常是以繫結 DN 來表示，例如 `CN=Directory Manager`。

* `serviceAccountPassword` 是用來連線到 LDAP 伺服器的密碼。 此值會儲存在先前設定的 Azure Key Vault 執行個體中。  

* `userAttributes` 定義要查詢的使用者相關屬性清單。 這些屬性稍後會對應至相對應的 Azure AD 屬性。

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>設定移轉工作流程

移轉工作流程組態會決定 Maverics 如何將使用者從 SiteMinder 或 LDAP 遷移到 Azure AD。

此工作流程會：
- 使用 SiteMinder 連接器，通過 Proxy 處理 SiteMinder 登入。 使用者認證會透過 SiteMinder 驗證進行驗證，然後傳遞至工作流程的後續步驟。
- 從 SiteMinder 使用者存放區取出使用者設定檔屬性。
- 向 Microsoft Graph API 提出要求，以在您的 Azure AD 租用戶中建立使用者。

若要設定移轉工作流程，請執行下列動作：

1. 為工作流程命名 (例如 **SiteMinder to Azure AD Migration**)。
1. 指定 `endpoint`，這是公開工作流程的 HTTP 路徑，會觸發該工作流程的 `actions` 以回應要求。 `endpoint` 通常會對應至代理的應用程式 (例如 `/my_app`)。 值必須同時包含前置和後置斜線。
1. 將適當的 `actions` 新增至工作流程。

   a. 定義 SiteMinder 連接器的 `login` 方法。 連接器值必須符合連接器組態中的名稱值。

   b. 定義 LDAP 連接器的 `getprofile` 方法。

   c.  定義 AzureAD 連接器的 `createuser` 方法。

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>驗證移轉工作流程

1. 如果 Maverics 服務尚未執行，請執行下列命令啟動： 

   `sudo systemctl start maverics`

1. 移至代理的登入 URL：`http://host.company.com/my_app`。
1. 提供用來登入應用程式的使用者認證，該應用程式受 SiteMinder 保護。
4. 移至 **首頁** > **使用者 | 所有使用者** 以確認已在 Azure AD 租用戶中建立使用者。  

### <a name="configure-the-session-abstraction-workflow"></a>設定工作階段抽象工作流程

工作階段抽象工作流程會將舊版內部部署 Web 應用程式的驗證和存取控制移動到 Azure AD 租用戶。

假設沒有任何工作階段存在，Azure 連接器會使用 `login` 方法，將使用者重新導向至登入 URL。

經過驗證之後，以結果形式建立的工作階段權杖會傳遞給 Maverics， 並使用 SiteMinder 連接器的 `emulate` 方法來模擬以 Cookie 為基礎的工作階段或以標頭為基礎的工作階段，然後使用應用程式所需的任何其他屬性來裝飾要求。

1. 為工作流程命名 (例如 **SiteMinder Session Abstraction**)。
1. 指定 `endpoint`，其通常會對應至代理的應用程式。 值必須同時包含前置和後置斜線 (例如 `/my_app/`)。
1. 將適當的 `actions` 新增至工作流程。

   a. 定義 Azure 連接器的 `login` 方法。 `connector` 值必須符合連接器組態中的 `name` 值。

   b. 定義 SiteMinder 連接器的 `emulate` 方法。

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>驗證工作階段抽象工作流程

1. 移至代理的應用程式 URL：`https://<AZURECOMPANY.COM>/<MY_APP>`。 
    
    系統會將您重新導向至代理的登入頁面。

1. 輸入 Azure AD 使用者認證。

   系統應該會將您重新導向至應用程式，就像是直接由 SiteMinder 驗證一樣。
