---
title: 開發安全的 web 應用程式 |Microsoft Docs
description: 這個簡單的範例應用程式會在您于 Azure 上開發時，實行可改善應用程式和組織安全性狀態的安全性最佳作法。
keywords: NA
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686800"
---
# <a name="develop-a-secure-web-app"></a>開發安全的 Web 應用程式

這個範例是簡單的 Python 應用程式，它會顯示一個網頁，其中包含在 Azure 上開發應用程式的安全性資源連結。 當您在 Azure 上開發應用程式時，應用程式會實行安全性最佳作法，協助改善您的應用程式和組織的安全性狀態。

您應遵循本文中所述的步驟，以確保應用程式元件已正確設定。 資料庫、Azure App Service、Azure Key Vault 實例和 Azure 應用程式閘道實例會彼此相依。

部署腳本會設定基礎結構。 執行部署腳本之後，您必須在 Azure 入口網站中進行一些手動設定，以將元件和服務連結在一起。

範例應用程式的目標是在 Azure 上開發應用程式的初學者，而他們想要在其應用程式中執行安全性措施。

在開發和部署此應用程式時，您將瞭解如何：

- 建立 Azure Key Vault 實例，並從中儲存和取出秘密。
- 部署適用於 PostgreSQL 的 Azure 資料庫、設定安全密碼及授權存取。
- 在適用于 Linux 的 Azure Web Apps 上執行 Alpine Linux 容器，並啟用適用于 Azure 資源的受控識別。
- 使用[OWASP 前10大規則集](https://coreruleset.org/)的防火牆建立並設定 Azure 應用程式閘道實例。
- 使用 Azure 服務，啟用傳輸中和待用資料的加密。

在您開發並部署此應用程式之後，您將會設定下列範例 web 應用程式，以及所述的設定和安全性量值。

![範例 web 應用程式](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>架構

應用程式是典型的多層式應用程式，具有三個層級。 整合了監視和密碼管理元件的前端、後端和資料庫層，如下所示：

![應用程式架構](./media/secure-web-app/architecture.png)

架構是由下列元件所組成：

- [Azure 應用程式閘道](../../application-gateway/index.yml)。 提供應用程式架構的閘道和防火牆。
- [Linux 上的 Azure Web Apps](../../app-service/containers/app-service-linux-intro.md)。 提供容器執行時間，以在 Linux 環境中執行 Python 應用程式。
- [Azure Key Vault](../../key-vault/index.yml)。 會儲存並加密我們的應用程式密碼，並管理其周圍的存取原則建立。
- [適用於 PostgreSQL 的 Azure 資料庫](https://azure.microsoft.com/services/postgresql/)。 安全地儲存應用程式的資料。
- [Azure 資訊安全中心](../../security-center/index.yml)和[Azure 應用程式深入](../../azure-monitor/app/app-insights-overview.md)解析。 提供應用程式作業的監視和警示。

## <a name="threat-model"></a>威脅模型

威脅模型化是識別企業和應用程式潛在安全性威脅的程式，然後確保適當的風險降低計畫已就緒。

這個範例使用[Microsoft Threat Modeling Tool](threat-modeling-tool.md)來執行安全範例應用程式的威脅模型化。 藉由將元件和資料流程的圖表化，您可以及早識別開發程式中的問題和威脅。 這可節省時間和金錢。

這是範例應用程式的威脅模型：

![威脅模型](./media/secure-web-app/threat-model.png)

下列螢幕擷取畫面顯示威脅模型化工具所產生的一些範例威脅和潛在弱點。 威脅模型概述公開的攻擊面，並提示開發人員思考如何減輕問題。

![威脅模型輸出](./media/secure-web-app/threat-model-output.png)

例如，藉由淨化使用者輸入以及在適用於 PostgreSQL 的 Azure 資料庫中使用預存函式，可減輕前述威脅模型輸出中的 SQL 插入式攻擊。 這項緩和措施可防止在資料讀取和寫入期間執行任意查詢。

開發人員藉由減少威脅模型輸出中的每個威脅，來改善系統的整體安全性。

## <a name="deployment"></a>部署

下列選項可讓您在 Azure App Service 上執行 Linux：

- 從 Azure 上預先建立的 Microsoft 容器清單中，選擇已使用支援技術（Python、Ruby、PHP、JAVA、node.js、.NET Core）建立的容器。
- 使用自訂建立的容器。 選取您自己的容器登錄作為映射的來源，並根據支援 HTTP 的許多可用技術來建立。

在此範例中，您將執行部署腳本，將 webapp 部署到 App Service 並建立資源。

應用程式可以使用如下所示的不同部署模型：

![部署資料流程圖](./media/secure-web-app/deployment.png)

有許多方式可以在 Azure 上部署應用程式，包括：

- Azure 資源管理員範本
- PowerShell
- Azure CLI
- Azure 入口網站
- Azure DevOps

使用此應用程式：

- [Docker](https://docs.docker.com/)來建立及建立容器映射。
- 部署的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 。
- [Docker Hub](https://hub.docker.com/)作為容器登錄。

## <a name="security-considerations"></a>安全性考量

### <a name="network"></a>網路

範例應用程式會針對流入和流出網路的傳輸中資料，使用端對端 TLS/SSL 加密。 閘道是以自我簽署憑證進行設定。
> [!IMPORTANT]
> 本示範中使用自我簽署憑證。 在生產環境中，您應該從已驗證的憑證授權單位單位（CA）取得憑證。

應用程式防火牆也會在網路流量中偵測到惡意流量時，檢查傳入流量和警示管理員。
應用程式閘道可降低在威脅模型中發現的 DDoS 和 SQL 插入式威脅的可能性。

### <a name="identity"></a>身分識別

若要登入入口網站，範例應用程式會針對獲指派資源存取權的 Azure Active Directory （Azure AD）系統管理員使用多重要素驗證。
範例應用程式會使用受控識別來取得從 Azure Key Vault 讀取和取出秘密的許可權，以確保應用程式不需要硬編碼認證和權杖來讀取秘密。 Azure AD 會自動建立服務主體，讓應用程式在使用受控識別時，需要讀取和修改秘密。

適用于 Azure 資源和 MFA 的受控識別，讓敵人更難在系統中取得許可權並提升其許可權。 威脅模型中指出此威脅。
應用程式會使用 OAuth，此功能可讓在 OAuth 應用程式中註冊的使用者登入應用程式。

### <a name="storage"></a>儲存體

適用於 PostgreSQL 的 Azure 資料庫會自動將于 postgresql 資料庫中的資料加密。 資料庫會授權 App Service 的 IP 位址，如此一來，只有已部署的 App Service web 應用程式才能使用正確的驗證認證來存取資料庫資源。

### <a name="logging-and-auditing"></a>記錄與稽核

應用程式會使用 Application Insights 來執行記錄，以追蹤所發生的計量、記錄和例外狀況。 此記錄會提供足夠的應用程式中繼資料，以通知開發人員和營運小組成員應用程式的狀態。 它也會在發生安全性事件時，提供足夠的資料來回溯。

## <a name="cost-considerations"></a>成本考量

如果您還沒有 Azure 帳戶，可以建立一個免費帳戶。 前往[免費帳戶頁面](https://azure.microsoft.com/free/)以開始使用、瞭解免費 Azure 帳戶可執行檔工作，以及學習哪些產品免費12個月。

若要使用安全性功能來部署範例應用程式中的資源，您必須支付一些 premium 功能。 當應用程式調整規模，且 Azure 所提供的免費層和試用版需要升級以符合應用程式需求時，您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估您的成本。

## <a name="deploy-the-solution"></a>部署解決方案

### <a name="prerequisites"></a>先決條件

若要讓應用程式啟動並執行，您需要安裝下列工具：

- 用來修改和查看應用程式程式碼的程式碼編輯器。[Visual Studio Code](https://code.visualstudio.com/)是一個開放原始碼選項。
- 在您的開發電腦上[Azure CLI](/cli/azure/install-azure-cli) 。
- 系統上的[Git](https://git-scm.com/) 。 Git 是用來在本機複製原始程式碼。
- [jq](https://stedolan.github.io/jq/)，這是用來以方便使用的方式查詢 JSON 的 UNIX 工具。

您需要 Azure 訂用帳戶，才能部署範例應用程式的資源。 如果您沒有 Azure 訂用帳戶，您可以[建立免費帳戶](https://azure.microsoft.com/free/)來測試範例應用程式。

安裝這些工具之後，您就可以開始在 Azure 上部署應用程式。

### <a name="environment-setup"></a>環境設定

執行部署腳本來設定環境和訂用帳戶：

1. 若要複製原始程式碼存放庫，請使用下列 Git 命令：

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. 若要移入目錄，請使用下列命令：

   ```shell
   cd tutorial-project/scripts
   ```

3. [腳本] 資料夾中有一些檔案是您所使用的平臺（Windows 或 Linux）所特有。 因為已安裝 Azure CLI，請在命令提示字元中執行下列 Azure CLI 命令以登入 Azure 帳戶：

   ```azurecli-interactive
   az login
   ```

瀏覽器將會開啟，並使用您的認證登入。 登入之後，您可以從命令提示字元開始部署資源。

部署腳本`deploy-powershell.ps1`和`deploy-bash.sh`包含部署整個應用程式的程式碼。
若要部署方案：

1. 如果您在 PowerShell 上執行`deploy-powershell.ps1`檔案，請輸入`./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME`將區域和資源組名取代為適當的 Azure 區域，並使用資源群組的名稱
2. 如果您在 Linux 上執行`deploy-bash.sh`檔案，請輸入`/deploy-bash.sh REGION RESOURCE_GROUP_NAME`，您可能必須輸入來建立檔案可執行檔`chmod +x deploy-bash.sh`

下列範例會展示主要元件的程式碼片段。 您可以藉由執行部署檔案，個別部署範例或與其余元件。

### <a name="implementation-guidance"></a>實作指引

部署腳本是一個可以細分成四個階段的腳本。 每個階段都會部署並設定[架構圖](#architecture)中的 Azure 資源。

四個階段為：

- 部署 Azure Key Vault。
- 部署適用於 PostgreSQL 的 Azure 資料庫。
- 在 Linux 上部署 Azure Web Apps。
- 使用 web 應用程式防火牆部署應用程式閘道。

每個階段都是使用先前部署的資源中的設定，以前面的一項為基礎。

若要完成執行步驟，請確定您已安裝 [[必要條件](#prerequisites)] 底下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure Key Vault

在本節中，您會建立及部署用來儲存秘密和憑證的 Azure Key Vault 實例。

完成部署之後，您會有一個部署在 Azure 上的 Azure Key Vault 實例。

若要使用 Azure CLI 部署 Azure Key Vault：

1. 宣告 Azure Key Vault 的變數。
2. 註冊 Azure Key Vault 提供者。
3. 建立實例的資源群組。
4. 在步驟3中建立的資源群組中建立 Azure Key Vault 實例。

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

最佳做法是在使用 Key Vault 來存取資源的應用程式中，使用適用于 Azure 資源的受控識別。 當 Key Vault 的存取金鑰不是以程式碼或在設定中儲存時，您的安全性狀態就會增加。

#### <a name="deploy-azure-database-for-postgresql"></a>部署適用於 PostgreSQL 的 Azure 資料庫

適用於 PostgreSQL 的 Azure 資料庫以下列方式運作，請先建立資料庫伺服器，然後建立用來儲存架構和資料的資料庫。

完成部署之後，您會有一個在 Azure 上執行的于 postgresql 伺服器和資料庫。

若要使用 Azure CLI 部署適用於 PostgreSQL 的 Azure 資料庫：

1. 使用 Azure CLI 和您的 Azure 訂用帳戶設定來開啟終端機。
2. 產生用來存取資料庫的安全使用者名稱和密碼組合。 （這些應該儲存在使用它們的應用程式 Azure Key Vault 中）。
3. 建立于 postgresql 伺服器實例。
4. 在您于步驟3建立的伺服器實例上建立資料庫。
5. 在於 postgresql 實例上執行于 postgresql 腳本。

下列程式碼會從上述的 [部署 KeyVault] 步驟中，依賴儲存在 Azure KeyVault 中的 PGUSERNAME 和 PGPASSWORD 秘密。

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

在您部署資料庫之後，您必須將其認證和連接字串儲存在 Azure Key Vault 中。
在 [腳本] 資料夾中，有`functions.sql`一個檔案包含 PL/pgSQL 程式碼，當您執行時，它會建立預存函式。 執行此檔案會參數化輸入，以限制 SQL 插入。

于 postgresql 是與名`psql`為的工具配套使用，用來連接到資料庫。 若要`functions.sql`執行，您必須從本機電腦連接到適用於 PostgreSQL 的 Azure 資料庫實例，並從該處執行。 Psql 工具的安裝包含在每個作業系統上的預設安裝中，以供于 postgresql 之用。
如需詳細資訊，請參閱[Psql 檔](https://www.postgresql.org/docs/9.3/app-psql.html)。

Azure Cloud Shell 也包含`psql`工具。 您可以藉由選取 Cloud Shell 圖示，直接從 Azure 入口網站使用 Cloud Shell。

若要啟用于 postgresql 實例的遠端存取，您必須在於 postgresql 中授權該 IP 位址。
若要啟用此存取權，請前往 [連線**安全性**] 索引標籤，選取 [**新增用戶端 IP**]，然後儲存新的設定。

![授權用戶端 IP](./media/secure-web-app/add-client-ip-postgres.png)

如果您使用 Cloud Shell 而不是本機 psql 工具，請選取 [**允許存取 Azure 服務**]，並將其值變更為 [**開啟**]，以允許您的 Cloud Shell 存取。

接著，從 Azure 入口網站上于 postgresql 實例的 [**連接**字串] 索引標籤中，執行下列 psql 命令，並連接到實例。
以資料庫的 [連接字串] 分頁中的參數取代空的大括弧，並將 [密碼] 從 [Azure Key Vault]。

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

確定連線到資料庫之後，請執行下列 PL/pgSQL 腳本。 此腳本會建立用來將資料插入資料庫中的預存函數。

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

如需有關如何為于 postgresql 設定 TLS 和憑證授權單位單位（CA）驗證的詳細資訊，請參閱[在適用於 PostgreSQL 的 Azure 資料庫中設定 tls 連線能力](/azure/postgresql/concepts-ssl-connection-security)。

容器中包含根憑證。 取得憑證所採取的步驟如下：

1. 從[憑證授權單位](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)單位下載憑證檔案。
2. [在您的電腦上下載並安裝 OpenSSL](/azure/postgresql/concepts-ssl-connection-security)。
3. 將憑證檔案解碼：

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

若要深入瞭解如何設定 TLS security for 于 postgresql，請參閱[設定 Tls 連接安全性](/azure/postgresql/concepts-ssl-connection-security)。

#### <a name="deploy-azure-web-apps-on-linux"></a>在 Linux 上部署 Azure Web Apps

您可以在 Azure App Service 上輕鬆地建立 Linux 服務，因為 Azure 提供一組預先建立的容器和映射，適用于 Python、Ruby、c # 和 JAVA 等廣泛使用的語言。 Azure 也支援自訂容器，可讓幾乎所有程式設計語言都能在 Azure App Service 平臺上執行。

正在部署的應用程式是簡單的 Python 應用程式，可在最新的 Ubuntu Linux 散發套件上執行。 它會分別連接到先前各節中建立的 Azure Key Vault 和于 postgresql 實例，以取得認證管理和資料儲存。

下列 Docker 檔案會在應用程式的根資料夾中提供：

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

上述 Dockerfile 是用來建立裝載于之 Azure Container Registry 上的容器`mcr.microsoft.com/samples/basic-linux-app`。

下列程式碼：

1. 宣告 App Service 實例的變數和名稱。
2. 建立 App Service 方案的資源群組。
3. 在 Linux 容器實例上布建 Azure Web Apps。
4. 啟用 web 應用程式容器的記錄功能。
5. 在容器的應用程式設定中設定一些應用程式設定。

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

此腳本會為 App Service 實例建立指派的身分識別，以便與 MSI 搭配使用以與 Azure Key Vault 進行互動，而不需要在程式碼或設定中硬編碼密碼。

移至入口網站中的 Azure Key Vault 實例，在 [存取原則] 索引標籤上授權指派的身分識別。選取 [**新增存取原則**]。 在 [**選取主體**] 底下，搜尋類似于所建立 App Service 實例名稱的應用程式名稱。
附加至應用程式的服務主體應該是可見的。 選取它並 [儲存存取原則] 頁面，如下列螢幕擷取畫面所示。

因為應用程式只需要取得金鑰，請選取 [密碼] 選項中的 [**取得**] 許可權，以允許存取，同時降低授與的許可權。

![Key Vault 存取原則](./media/secure-web-app/kv-access-policy.png)

*建立 Key Vault 存取原則*

儲存存取原則，然後在 [**存取原則**] 索引標籤上儲存新的變更，以更新原則。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>部署已啟用 web 應用程式防火牆的應用程式閘道

在 web 應用程式中，不建議您直接將服務公開給網際網路上的外部世界。
負載平衡和防火牆規則可讓您更安全地控制連入流量，並協助您進行管理。

若要部署應用程式閘道實例：

1. 建立資源群組以存放應用程式閘道。
2. 布建要附加至閘道的虛擬網路。
3. 為虛擬網路中的閘道建立子網。
4. 布建公用 IP 位址。
5. 布建應用程式閘道。
6. 在閘道上啟用 web 應用程式防火牆。

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

上述腳本：

1. 在 Azure 上建立新的自我簽署憑證。
2. 下載自我簽署憑證做為 base64 編碼的檔案。
3. 產生自我簽署憑證的密碼。
4. 將憑證匯出為以密碼簽署的 PFX 檔案。
5. 將憑證的密碼儲存在 Azure Key Vault 中。

這一節會部署應用程式閘道：

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

完成部署之後，您會有已啟用 web 應用程式防火牆的應用程式閘道。

閘道實例會針對 HTTPS 公開端口443。 此設定可確保我們的應用程式只能透過 HTTPS 在埠443上存取。

封鎖未使用的埠並限制受攻擊面的風險，是安全性最佳作法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>將網路安全性群組新增至 App Service 實例

App Service 實例可以與虛擬網路整合。 此整合可讓您設定網路安全性群組原則，以管理應用程式的傳入和傳出流量。

1. 若要啟用這項功能，請在 [Azure App 服務實例] 分頁的 [**設定**] 底下，選取 [**網路**]。 在右窗格的 [ **VNet 整合**] 底下，選取 [**按一下這裡進行設定**]。

   ![新增虛擬網路整合](./media/secure-web-app/app-vnet-menu.png)

    *App Service 的新虛擬網路整合*

1. 在下一個頁面上，選取 [**新增 VNET （預覽）**]。

1. 在下一個功能表中，選取以開頭的部署中建立的虛擬網路`hello-vnet`。 您可以建立新的子網，或選取現有的子網。
   在此情況下，請建立新的子網。 將 [**位址範圍**] 設定為 [ **10.0.3.0/24** ]，並將子網命名為**應用程式子網**。

   ![App Service 虛擬網路設定](./media/secure-web-app/app-vnet-config.png)

    *App Service 的虛擬網路設定*

現在您已啟用虛擬網路整合，您可以將網路安全性群組新增至我們的應用程式。

1. 使用 [搜尋] 方塊，搜尋 [**網路安全性群組**]。 在結果中選取 [**網路安全性群組**]。

    ![搜尋網路安全性群組](./media/secure-web-app/nsg-search-menu.png)

    *搜尋網路安全性群組*

2. 在下一個功能表上，選取 [**新增**]。 輸入 NSG 的**名稱**及其所在的**資源群組**。 此 NSG 將會套用至應用程式閘道的子網。

    ![建立 NSG](./media/secure-web-app/nsg-create-new.png)

    *建立 NSG*

3. 建立 NSG 之後，請選取它。 在其分頁的 [**設定**] 底下，選取 [**輸入安全性規則**]。 設定這些設定，以允許透過埠443進入應用程式閘道的連線。

   ![設定 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *設定 NSG*

4. 在 [閘道 NSG] 的輸出規則中，新增規則以允許對 App Service 實例的輸出連線，方法是建立以服務標籤`AppService`為目標的規則：

   ![新增 NSG 的輸出規則](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *新增 NSG 的輸出規則*

    新增另一個輸出規則，以允許閘道將輸出規則傳送到虛擬網路。

   ![新增另一個輸出規則](./media/secure-web-app/nsg-outbound-vnet.png)

    *新增另一個輸出規則*

5. 在 NSG 的 [子網] 分頁上，選取 [**關聯**]，選取部署中所建立的虛擬網路，然後選取名為**gw-subnet**的閘道子網。 NSG 會套用至子網。

6. 建立另一個 NSG，如同在先前的步驟中，這次適用于 App Service 實例。 為它命名。 新增埠443的輸入規則，如同您針對應用程式閘道 NSG 所做的一樣。

   如果您將 App Service 實例部署在 App Service 環境實例上（這不是此應用程式的情況），您可以在 App Service NSG 的輸入安全性群組上開啟埠454-455，以新增輸入規則以允許 Azure 服務健康狀態探查。 設定如下：

   ![新增 Azure 服務健康狀態探查的規則](./media/secure-web-app/nsg-create-healthprobes.png)

    *新增 Azure 服務健康狀態探查的規則（僅 App Service 環境）*

7. 在 [輸出安全性規則] 中，建立新的輸出安全性規則，以允許 App Service 實例與于 postgresql 資料庫進行通訊。 如下所示加以設定：

   ![允許輸出于 postgresql 連接的規則](./media/secure-web-app/nsg-outbound-postgresql.png)

   *新增規則以允許輸出于 postgresql 連線*

若要限制受攻擊面，請修改 App Service 網路設定，只允許應用程式閘道存取應用程式。
若要這麼做，請前往 [App Service 網路] 索引標籤，選取 [ **IP 限制**] 索引標籤，然後建立允許僅允許應用程式閘道 IP 直接存取服務的允許規則。

您可以從其 [總覽] 頁面取得閘道的 IP 位址。 在 [ **Ip 位址 CIDR** ] 索引標籤上，以下列格式輸入`<GATEWAY_IP_ADDRESS>/32`ip 位址：。

![僅允許閘道](./media/secure-web-app/app-allow-gw-only.png)

*僅允許閘道 IP 存取 App Service*

#### <a name="implement-azure-active-directory-oauth"></a>執行 OAuth Azure Active Directory

在範例 web 應用程式頁面上散發的 Azure 檔是我們的應用程式中可能需要保護的資源。 您可以使用 Azure Active Directory （Azure AD），透過不同的驗證流程來為 web、桌面和行動應用程式執行驗證。
應用程式會使用**與 Microsoft 的登**入，讓應用程式能夠讀取已新增至單一租使用者 Azure AD 使用者清單中的使用者設定檔。

在 Azure 入口網站中，將應用程式設定為使用所需的認證：

1. 選取 [ **Azure Active Directory**]，或使用 [搜尋] 方塊進行搜尋。

2. 選取 [**新增註冊**]：

   ![建立註冊](./media/secure-web-app/ad-auth-create.png)

   *建立 Azure AD 應用程式註冊*

3. 在下一個頁面上，輸入應用程式名稱。 在 [**支援的帳戶類型**] 底下，選取 [**僅此組織目錄中的帳戶**]。
    在 [重新**導向 URI**] 底下，輸入應用程式將在其上執行的基底網域，再加上一個具有權杖端點的主域。 例如： *GATEWAY_HASH*. cloudapp.net/token。

   ![設定 Azure AD 應用程式註冊](./media/secure-web-app/ad-auth-type.png)

   *設定 Azure AD 應用程式註冊*

4. 您會看到一個畫面，其中顯示已註冊的應用程式及其資訊。 您必須將此資訊新增至 Azure Key Vault 實例。
   1. 複製應用程式（用戶端）識別碼，並將它儲存`CLIENTID`在 Key Vault 中。
   2. 複製您在上一個步驟中輸入的 [重新導向 URI]， `REDIRECTURI`並將它儲存為。
   3. 複製 Azure AD 預設目錄名稱 *，格式為*microsoftonline.com，並將它儲存在 Key Vault 中`TENANT`。
   4. 移至您先前建立之 Azure AD 應用程式的 [**憑證 & 秘密**] 索引標籤，然後選取 [**新增用戶端密碼**]，如下列螢幕擷取畫面所示。 設定到期日，然後複製產生的值，並將其儲存在 Key Vault 中`CLIENTSECRET`。

      ![Azure AD 授權密碼](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 授權密碼*

   5. 使用任何命令列/線上工具來產生安全的隨機秘密金鑰。 將它儲存到 Key Vault `FLASKSECRETKEY`做為。 應用程式架構會使用此金鑰來建立會話。
        若要瞭解如何產生秘密金鑰，請參閱[Flask 會話](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)。

5. 設定登入之後，您必須將使用者新增至 Azure AD 連結，讓他們能夠登入資源。 若要新增它們，請移至 Azure AD 中的 [**使用者**] 索引標籤，選取 [**所有使用者**]，然後選取 [**新增使用者**] 或 [**新增來賓使用者**]。 若要進行測試，您可以新增來賓使用者，並將使用者邀請到目錄中。 或者，如果應用程式執行所在的網域已通過驗證，您可以新增使用者。 在此範例中，只有在 Azure AD 租使用者中註冊的使用者可以註冊以進行存取。 如需多租使用者登入存取的詳細資訊，請參閱檔集。

   ![將使用者新增至預設網域](./media/secure-web-app/ad-auth-add-user.png)

   *將使用者新增至預設 Azure Active Directory 網域*

將 Azure AD 設定和密碼新增至 Key Vault 之後，使用者即可使用 Azure OAuth 驗證向應用程式進行驗證。
在應用程式程式碼中，這是由 Azure Active Directory Authentication Library （ADAL）處理。

在 Key Vault 秘密之後，且應用程式可以存取秘密和資料庫之後，即可透過閘道的應用程式 URL （HTTPs：\//GATEWAY_HASH. cloudapp）來連線應用程式服務，您可以從其分頁取得。

如果您在登入 Azure AD 時收到錯誤訊息，指出「使用者未在您嘗試登入的目錄中註冊」，您需要新增使用者。 若要新增使用者，請移至 Azure AD 的 [**使用者**] 索引標籤，然後輸入使用者的詳細資料或邀請使用者，輸入其電子郵件地址作為 [**邀請來賓**] 分頁中 Azure AD 的來賓使用者，藉以手動新增使用者。

#### <a name="deploy-application-insights"></a>部署 Application Insights
現在應用程式已部署且正常運作，您需要處理在應用程式中發生的錯誤，以及記錄和追蹤資料收集。
記錄和追蹤資料收集可讓您查看應用程式中所發生的 audit 事件。

Application Insights 是一項服務，會收集可由使用者或系統產生的記錄檔。

若要建立 Application Insights 實例：

1. 使用 Azure 入口網站中的 [搜尋] 方塊來搜尋**Application Insights** 。
2. 選取 [Application Insights] ****。 提供此處顯示的詳細資料，以建立實例。

   ![建立 Application Insights 實例](./media/secure-web-app/app-insights-data.png)

部署完成之後，您會有一個 Application Insights 實例。

建立 Application Insights 實例之後，您必須讓應用程式知道可讓它將記錄傳送至雲端的檢測金鑰。 若要這麼做，請在 Azure 為 Application Insights 提供的應用程式程式庫中抓取 Application Insights 金鑰並加以使用。 最佳做法是將金鑰和秘密儲存在 Azure Key Vault 中，以確保其安全。

針對基本範例應用程式，在您建立 Application Insights 實例之後，您必須讓應用程式知道可讓它將記錄傳送至雲端的檢測金鑰。
在 Key Vault 中，設定`APPINSIGHTSKEY`密碼並將其值設定為檢測金鑰。 這麼做可讓應用程式將記錄和計量傳送至 Application Insights。

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>執行 Azure Active Directory 的多重要素驗證

系統管理員必須確保入口網站中的訂用帳戶帳戶受到保護。 此訂用帳戶容易遭受攻擊，因為它會管理您所建立的資源。 若要保護訂用帳戶，請在訂用帳戶的 [ **Azure Active Directory** ] 索引標籤上啟用多重要素驗證。

Azure AD 會根據套用至符合特定準則的使用者或使用者群組的原則進行操作。
Azure 會建立預設原則，指定系統管理員需要雙因素驗證來登入入口網站。
啟用此原則之後，系統可能會提示您登出，然後重新登入 Azure 入口網站。

若要啟用管理登入的 MFA：

1. 移至 Azure 入口網站中的 [ **Azure Active Directory** ] 索引標籤
2. 在 [安全性] 類別底下，選取 [條件式存取]。 您會看到這個畫面：

   ![條件式存取-原則](./media/secure-web-app/ad-mfa-conditional-add.png)

如果您無法建立新的原則：

1. 移至 [ **MFA** ] 索引標籤。
2. 選取 [Azure AD Premium**免費試用**] 連結以訂閱免費試用版。

   ![Azure AD Premium 免費試用](./media/secure-web-app/ad-trial-premium.png)

返回 [條件式存取] 畫面。

1. 選取 [新增原則] 索引標籤。
2. 輸入原則名稱。
3. 選取您想要啟用 MFA 的使用者或群組。
4. 在 [**存取控制**] 底下，選取 [**授**與] 索引標籤，然後選取 [**需要多重要素驗證**] \ （以及其他設定，如果需要的話）。

   ![需要 MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

您可以選取畫面頂端的核取方塊來啟用原則，或在 [**條件式存取**] 索引標籤上執行此動作。當原則啟用時，使用者需要 MFA 才能登入入口網站。

所有 Azure 系統管理員都需要使用 MFA 的基準原則。 您可以立即在入口網站中啟用它。 啟用此原則可能會使目前的會話無效，並強制您重新登入。

如果未啟用基準原則：

1. 選取 [**需要系統管理員的 MFA**]。
2. 選取 [**立即使用原則**]。

   ![選取 [立即使用原則]](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure Sentinel 來監視應用程式和資源

隨著應用程式成長，就難以匯總從資源接收的所有安全性信號和計量，並使其以動作導向的方式發揮作用。

Azure Sentinel 的設計是用來收集資料、偵測可能的威脅類型，以及提供安全性事件的可見度。
雖然它會等待手動操作，但 Azure Sentinel 可以依賴預先撰寫的操作手冊來啟動警示和事件管理流程。

範例應用程式是由 Azure Sentinel 可以監視的數個資源所組成。
若要設定 Azure Sentinel，您必須先建立 Log Analytics 工作區，以儲存從各種資源收集而來的所有資料。

若要建立此工作區：

1. 在 [Azure 入口網站的 [搜尋] 方塊中，搜尋**Log Analytics**。 選取 [Log Analytics 工作區]****。

   ![搜尋 Log Analytics 工作區](./media/secure-web-app/sentinel-log-analytics.png)

    *搜尋 Log Analytics 工作區*

2. 在下一個頁面上，選取 [**新增**]，然後提供工作區的名稱、資源群組和位置。
   ![建立 Log Analytics 工作區](./media/secure-web-app/sentinel-log-analytics-create.png)

   *建立 Log Analytics 工作區*

3. 使用 [搜尋] 方塊來搜尋**Azure Sentinel**。

   ![搜尋 Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *搜尋 Azure Sentinel*

4. 選取 [**新增**]，然後選取您稍早建立的 Log Analytics 工作區。

   ![新增 Log Analytics 工作區](./media/secure-web-app/sentinel-workspace-add.png)

    *新增 Log Analytics 工作區*

5. 在 [ **Azure Sentinel-資料連線器**] 頁面的 [設定 **] 下，** 選取 [**資料連線器**]。 您會看到一組 Azure 服務，您可以連結到 Log Analytics 儲存體實例，以便在 Azure Sentinel 中進行分析。

   ![Log Analytics 資料連線器](./media/secure-web-app/sentinel-connectors.png)

    *將資料連線器新增至 Azure Sentinel*

   例如，若要連接應用程式閘道，請執行下列步驟：

   1. 開啟 [Azure 應用程式閘道實例] 分頁。
   2. 在 [監視]**** 下方，選取 [診斷設定]****。
   3. 選取 [**新增診斷設定**]。

      ![新增應用程式閘道診斷](./media/secure-web-app/sentinel-gateway-connector.png)

      *新增應用程式閘道診斷*

   4. 在 [**診斷設定**] 頁面上，選取您所建立的 Log Analytics 工作區，然後選取您想要收集並傳送至 Azure Sentinel 的所有計量。 選取 [儲存]  。

        ![Azure Sentinel 連接器設定](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel 連接器設定*

  資源中的計量位於 Azure Sentinel 中，您可以在其中查詢和調查它們。

   在 Azure Key Vault 的診斷設定、公用 IP 位址、適用於 PostgreSQL 的 Azure 資料庫，以及支援您帳戶中診斷記錄的任何服務中，新增相同的計量。

設定計量之後，Azure Sentinel 有要分析的資料。

## <a name="evaluate-and-verify"></a>評估和驗證

開發和部署架構之後，您必須確定程式碼和部署的服務符合安全性標準。 以下是您可以用來驗證軟體的一些步驟：

- 靜態程式碼分析
- 弱點掃描
- 尋找和修正應用程式相依性中的弱點

這些是用於安全開發之最佳作法的基本組建區塊。

### <a name="static-code-analysis"></a>靜態程式碼分析

針對範例應用程式，使用靜態分析工具進行驗證時，需要使用污點檢查和資料流程分析等技術，來尋找應用程式代碼中的弱點。 Python 靜態分析工具可讓您更安心地保護應用程式的安全。

**進行 Lint 檢查**

PyFlakes 是 Python linting 程式庫，可協助您從應用程式移除無作用的程式碼和未使用的函式，如下所示：

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting 提供提示和可能的變更，讓您的程式碼更簡潔且更容易在執行時間發生錯誤。

**PyLint**

PyLint 為此專案提供最大的值。 它會執行程式碼標準檢查、錯誤檢查和重構秘訣，以確保在伺服器上執行的程式碼是安全的。 藉由使用 PyLint 來更新您的程式碼，您可以消除 bug 並改善 PyLint 評等，如下圖所示。

![PyLint 之前](./media/secure-web-app/before-pylint.png)

*PyLint 之前*

修正 linting 工具所找到的部分程式碼錯誤之後，您就有更多的信心讓程式碼不容易發生錯誤。 修正錯誤可大幅降低將程式碼部署到生產環境時可能發生的安全性風險。

![Pylint 之後](./media/secure-web-app/after-pylint.png)

*PyLint 之後*

### <a name="vulnerability-scanning"></a>弱點掃描

[OWASP 的 ZAP](https://www.zaproxy.org/)工具是一個開放原始碼 web 應用程式弱點掃描器，可用來檢查範例應用程式的弱點。 在範例應用程式上執行此工具，會顯示一些可能的錯誤和攻擊媒介。

![ZAP 工具](./media/secure-web-app/zap-tool.png)

*ZAP 工具*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>尋找並修正應用程式相依性中的弱點

若要尋找並修正應用程式相依性，您可以使用[OWASP 的](https://owasp.org/www-project-dependency-check/)相依性檢查。

安全性是檢查相依性的類似應用程式。 您可以在[GitHub](https://github.com/pyupio/safety)上找到它。 安全掃描在已知的弱點資料庫中找到的弱點。

![安全性](./media/secure-web-app/pysafety.png)

*安全性*

## <a name="next-steps"></a>後續步驟

下列文章可協助您設計、開發和部署安全的應用程式。

- [設計](secure-design.md)
- [開發](secure-develop.md)
- [部署](secure-deploy.md)
