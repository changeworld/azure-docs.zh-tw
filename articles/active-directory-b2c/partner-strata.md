---
title: 使用分層設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 whoIam 以進行使用者驗證
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: bddc4c64feb31f78bed482bbd729ab1c4b8e676e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171410"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>使用分層擴充 Azure AD B2C 以保護內部部署應用程式的教學課程

在此範例教學課程中，您將瞭解如何整合 Azure Active Directory (AD) B2C 與階層式 [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)。
Maverics Identity Orchestrator 擴充 Azure AD B2C，以保護內部部署應用程式。 它會連接到任何身分識別系統、以透明方式遷移使用者和認證、同步處理原則和設定，以及抽象化驗證和會話管理。 使用分層企業可以從舊版快速轉換成 Azure AD B2C，而不需要重寫應用程式。 此解決方案有下列優點：

- **客戶單一 Sign-On (內部部署混合式應用程式的 sso)**： Azure AD B2C 支援使用 Maverics Identity Orchestrator 的客戶 sso。 使用者以 Azure AD B2C 或社交識別提供者所裝載的帳戶登入 (IdP) 。 Maverics 會將 SSO 延伸至過去受舊版身分識別系統（例如 Symantec SiteMinder）保護的應用程式。

- 將 **以標準為基礎的 SSO 擴充至應用程式，而不重寫它們**：使用 Azure AD B2C 管理使用者存取權，以及啟用 Maverics IDENTITY Orchestrator SAML 或 OIDC 連接器的 sso。

- **簡易** 設定： Azure AD B2C 提供簡單的逐步使用者介面，可將 Maverics IDENTITY Orchestrator SAML 或 OIDC 連接器連線到 Azure AD B2C。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的 [Azure AD B2C 租](./tutorial-create-tenant.md) 使用者。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)的實例，用來儲存 Maverics Identity Orchestrator 所使用的秘密。 它是用來連接到 Azure AD B2C 或其他屬性提供者，例如輕量型目錄存取協定 (LDAP) 目錄或資料庫。

- 在 Azure 虛擬機器或您選擇的內部部署伺服器中安裝和執行的 [Maverics 身分識別協調](https://www.strata.io/maverics-identity-orchestrator/) 器實例。 如需如何取得安裝和設定檔的軟體和存取權的相關資訊，請聯絡 [分層](https://www.strata.io/contact/)

- 您將從舊版身分識別系統轉換成 Azure AD B2C 的內部部署應用程式。

## <a name="scenario-description"></a>案例描述

階層式 Maverics 整合包含下列元件：

- **Azure AD B2C**：負責驗證使用者認證的授權伺服器。 經過驗證的使用者可以使用儲存在 Azure AD B2C 目錄中的本機帳戶來存取內部部署應用程式。

- **外部社交或企業 IdP**：可以是任何 OpenID Connect 提供者、Facebook、Google 或 GitHub。 請參閱使用 [外部 idp](./technical-overview.md#external-identity-providers) 搭配 Azure AD B2C 的資訊。  

- **階層式 Maverics Identity Orchestrator**：協調使用者登入，並透過 HTTP 標頭以透明方式將身分識別傳遞給應用程式的服務。

下列架構圖顯示執行。

![影像顯示 Azure AD B2C 與分層 Maverics 整合的架構，以啟用混合式應用程式的存取](./media/partner-strata/strata-architecture-diagram.png)

| 步驟 | 描述 |
|:-------|:---------------|
| 1. | 使用者會要求存取內部部署裝載的應用程式。 Maverics Identity Orchestrator 會將使用者對應用程式提出的要求進行 proxy。|
| 2. | 協調器會檢查使用者的驗證狀態。 如果未收到會話權杖，或提供的會話權杖無效，則會將使用者傳送至 Azure AD B2C 進行驗證。|
| 3. | Azure AD B2C 會將驗證要求傳送至設定的社交 IdP。|
| 4. | IdP 會挑戰使用者的認證。 根據 IdP，使用者可能需要 (MFA) 進行多重要素驗證。|
| 5. | IdP 會將驗證回應傳回 Azure AD B2C。 （選擇性）使用者可以在此步驟期間，在 Azure AD B2C 目錄中建立本機帳戶。|
| 6. | Azure AD B2C 會將使用者要求傳送至 Azure AD B2C 租使用者中 Orchestrator 應用程式註冊期間指定的端點。|
| 7. | 協調器會評估存取原則，並計算要在轉送到應用程式的 HTTP 標頭中包含的屬性值。 在這個步驟中，協調器可能會向外呼叫其他屬性提供者，以取得正確設定標頭值所需的資訊。 Orchestrator 會設定標頭值，並將要求傳送至應用程式。|
| 8. | 使用者現在已通過驗證，並可存取應用程式。|

## <a name="get-maverics-identity-orchestrator"></a>取得 Maverics Identity Orchestrator
若要取得您將用來整合舊版內部部署應用程式與 Azure AD B2C 的軟體，請 [分層](https://www.strata.io/contact/)。 取得軟體之後，請依照下列步驟來判斷 Orchestrator 特定的必要條件，並執行必要的安裝和設定步驟。

## <a name="configure-your-azure-ad-b2c-tenant"></a>設定您的 Azure AD B2C 租使用者

1. **註冊您的應用程式**

   a. 在 Azure AD B2C 租使用者中，[將 Orchestrator 註冊為應用程式](./tutorial-register-applications.md?tabs=app-reg-ga)。
   >[!Note]
   >稍後當您設定 Orchestrator 實例時，您將需要租使用者名稱和識別碼、用戶端識別碼、用戶端密碼、已設定的宣告，以及重新導向 URI。

   b. 將 Microsoft MS 圖形 API 許可權授與您的應用程式。 您的應用程式將需要下列許可權： `offline_access` 、 `openid` 。

   c. 為您的應用程式新增重新導向 URI。 此 URI 將符合 `oauthRedirectURL` Orchestrator Azure AD B2C 連接器設定的參數，例如 `https://example.com/oidc-endpoint` 。

2. **建立使用者流程**：建立 [簽署和登入使用者流程](./tutorial-create-user-flows.md)。

3. **新增 IdP**：選擇使用本機帳戶或社交或企業 [IdP](./tutorial-add-identity-providers.md)登入您的使用者。

4. **定義使用者屬性**：定義要在註冊期間收集的屬性。

5. **指定應用程式宣告**：指定要透過 Orchestrator 實例傳回給應用程式的屬性。 協調器會使用 Azure AD B2C 所傳回之宣告的屬性，而且可以從其他已連線的身分識別系統（例如 LDAP 目錄和資料庫）抓取其他屬性。 這些屬性會在 HTTP 標頭中設定，並傳送至上游內部部署應用程式。

## <a name="configure-maverics-identity-orchestrator"></a>設定 Maverics Identity Orchestrator

在下列各節中，我們將逐步引導您完成設定 Orchestrator 實例所需的步驟。 如需其他支援和檔，請聯絡 [分層](https://www.strata.io/contact/)。

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator 伺服器需求

您可以透過 Azure、AWS 或 GCP 之類的提供者，在任何伺服器上執行 Orchestrator 實例，不論是內部部署或在公用雲端基礎結構中。

- OS： REHL 7.7 或更高版本，CentOS 7 +

- 磁片： 10 GB (小型) 

- 記憶體： 16 GB

- 埠： 22 (SSH/SCP) 、443、80

- 安裝/管理工作的根存取

- Maverics Identity Orchestrator `maverics` 以使用者身分執行 `systemd`

- 從裝載 Maverics Identity Orchestrator 的伺服器進行網路輸出，且能夠連線到您的 Azure AD 租使用者。

### <a name="install-maverics-identity-orchestrator"></a>安裝 Maverics Identity Orchestrator

1. 取得最新的 Maverics RPM 套件。 將套件放在您要安裝 Maverics 的系統上。 如果您要將檔案複製到遠端主機， [SCP](https://www.ssh.com/ssh/scp/) 是一個很有用的工具。

2. 若要安裝 Maverics 套件，請執行下列命令來取代您的檔案名 `maverics.rpm` 。

   `sudo rpm -Uvf maverics.rpm`

   依預設，Maverics 會安裝在 `/usr/local/bin` 目錄中。

3. 安裝 Maverics 後，其會以服務的形式在 `systemd` 下執行。  若要確認 Maverics 服務是否正在執行，請執行下列命令：

   `sudo service maverics status`

  如果 Orchestrator 安裝成功，您應該會看到類似下面的訊息：

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. 如果 Maverics 服務無法啟動，請執行下列命令來調查問題：

   `journalctl --unit=maverics.service --reverse`

   最新的記錄專案將會出現在輸出的開頭。

安裝 Maverics 後，系統會在 `/etc/maverics` 目錄中建立預設 `maverics.yaml` 檔案。

設定您的協調器以保護應用程式。 從 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9)整合 Azure AD B2C、儲存和取出秘密。 定義 Orchestrator 應從中讀取其設定的位置。

### <a name="supply-configuration-using-environment-variables"></a>使用環境變數提供設定

透過環境變數將設定提供給 Orchestrator 實例。

`MAVERICS_CONFIG`

此環境變數會告知 Orchestrator 實例，YAML 要使用的設定檔，以及在啟動或重新開機時要在哪裡找到它們。 在中設定環境變數 `/etc/maverics/maverics.env` 。

### <a name="create-the-orchestrators-tls-configuration"></a>建立 Orchestrator 的 TLS 設定

`tls`中的欄位 `maverics.yaml` 會宣告 Orchestrator 實例將使用的傳輸層安全性設定。 連接器可以使用 TLS 物件和 Orchestrator 伺服器。

`maverics` 金鑰會保留給 Orchestrator 伺服器。 所有其他金鑰都可供使用，而且可以用來將 TLS 物件插入指定的連接器中。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>設定 Azure AD B2C 連接器

協調器使用連接器來與驗證和屬性提供者整合。 在此情況下，此協調器應用程式閘道會使用 Azure AD B2C 連接器作為驗證和屬性提供者。 Azure AD B2C 使用社交 IdP 進行驗證，然後作為 Orchestrator 的屬性提供者，傳遞 HTTP 標頭中設定的宣告屬性。  

此連接器的設定會對應到在 Azure AD B2C 租使用者中註冊的應用程式。

1. 從您租使用者中的應用程式設定複製用戶端識別碼、秘密和重新導向 URI。

2. 為您的連接器提供名稱（如下所示 `azureADB2C` ），並將連接器設定 `type` 為 `azure` 。 記下連接器名稱，因為此值會用於以下的其他設定參數。

3. 針對此整合， `authType` 應該將設定為 `oidc` 。

4. 將您在步驟1中複製的用戶端識別碼設定為參數的值 `oauthClientID` 。

5. 將您在步驟1中複製的用戶端密碼設定為參數的值 `oauthClientSecret` 。

6. 將您在步驟1中複製的重新導向 URI 設定為參數的值 `oauthRedirectURL` 。

7. Azure AD B2C OIDC 連接器會使用知名的 OIDC 端點來探索中繼資料，包括 Url 和簽署金鑰。 將的值設定 `oidcWellKnownURL` 為您租使用者的端點。

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>將 Azure AD B2C 定義為您的驗證提供者

驗證提供者會決定如何針對未在應用程式資源要求中呈現有效會話的使用者進行驗證。 您 Azure AD B2C 租使用者中的設定會決定如何挑戰使用者的認證，並套用其他驗證原則。 例如，若要要求第二個因素來完成驗證程式，並決定驗證成功後應將哪些宣告傳回給 Orchestrator 應用程式閘道。

的值 `authProvider` 必須符合您連接器的 `name` 值。

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>使用 Orchestrator 應用程式閘道保護您的內部部署應用程式

Orchestrator 的應用程式閘道設定會宣告 Azure AD B2C 應如何保護您的應用程式，以及使用者應該如何存取應用程式。

1. 建立應用程式閘道的名稱。 您可以使用好記的名稱或完整的主機名稱作為應用程式的識別碼。

2. 設定 `location`。 不過，此處的範例會使用應用 `/` 程式的根目錄，可以是應用程式的任何 URL 路徑。

3. 使用 host： port 慣例定義中受保護的應用程式 `upstream` ： `https://example.com:8080` 。

4. 設定錯誤和未經授權頁面的值。

5. 定義必須提供給應用程式的 HTTP 標頭名稱和屬性值，以建立應用程式的驗證和控制存取權。 標頭名稱是任意的，通常會對應到應用程式的設定。 提供屬性值的連接器會命名空間屬性值。 在下列範例中，從 Azure AD B2C 傳回的值前面會加上連接器名稱， `azureADB2C` 其中尾碼是包含必要值的屬性名稱，例如 `given_name` 。

6. 設定要評估和強制執行的原則。 定義了三個動作： `allowUnauthenticated` 、 `allowAnyAuthenticated` 和 `allowIfAny` 。 每個動作都會與相關聯 `resource` ，而且會評估該原則 `resource` 。

>[!NOTE]
>`headers`和都 `policies` 使用 JavaScript 或 GoLang 服務延伸模組來執行可大幅增強預設功能的任意邏輯。

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>使用 Azure Key Vault 作為祕密提供者

請務必保護您 Orchestrator 用來連線到 Azure AD B2C 和任何其他身分識別系統的秘密。 在本教學課程中，Maverics 會預設為以純文字載入密碼， `maverics.yaml` 不過，在本教學課程中，您將使用 Azure Key Vault 作為秘密提供者。

請依照指示 [建立新的 Key Vault](../key-vault/secrets/quick-create-portal.md) ，讓您的協調器實例會將其作為秘密提供者使用。 將您的秘密新增至保存庫，並記下 `SECRET NAME` 提供給每個秘密的。 例如 `AzureADB2CClientSecret`。

若要將值宣告為 `maverics.yaml` 組態檔中的密碼，請使用角括弧包裝祕密：

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

在角括弧內指定的值必須對應至 `SECRET NAME` 您 Azure Key Vault 中的指定密碼。

若要從 Azure Key Vault 載入密碼，請使用下列模式，在檔案中設定環境變數，並在檔案中 `MAVERICS_SECRET_PROVIDER` `/etc/maverics/maverics.env` 找到 azure-credentials.js的認證：

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>合併所有內容

以下是當您完成以上所述的設定時，Orchestrator 的設定將如何顯示。

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>測試流程

1. 流覽至內部部署應用程式 url， `https://example.com/sonar/dashboard` 。

2. 協調器應重新導向至您在使用者流程中設定的頁面。

3. 從頁面上的清單中選取 IdP。

4. 將您重新導向至 IdP 之後，請視需要提供您的認證，包括該 IdP 需要的 MFA 權杖。

5. 成功驗證之後，您應該重新導向至 Azure AD B2C，以將應用程式要求轉送至 Orchestrator 重新導向 URI。

6. 協調器會評估原則、計算標頭，並將使用者傳送到上游應用程式。  

7. 您應該會看到要求的應用程式。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](./custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](./custom-policy-get-started.md?tabs=applications)