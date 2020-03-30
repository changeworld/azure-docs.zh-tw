---
title: 開發安全的 Web 應用程式 |微軟文檔
description: 此簡單示例應用實現了安全最佳實踐，在 Azure 上開發時可改進應用程式和組織的安全狀態。
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
ms.openlocfilehash: 75890efebc42b74c56fb95ed1803152b516588b9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385209"
---
# <a name="develop-a-secure-web-app"></a>開發安全的 Web 應用程式

此示例是一個簡單的 Python 應用，它顯示一個網頁，其中包含指向在 Azure 上開發應用的安全資源的連結。 該應用程式實現了安全最佳實踐，這些最佳實踐可説明改進應用程式和組織在 Azure 上開發應用時的安全狀況。

您應該按順序按照本文中描述的步驟操作，以確保正確配置應用程式元件。 資料庫、Azure 應用服務、Azure 金鑰保存庫實例和 Azure 應用程式閘道實例彼此依賴。

部署腳本設置基礎結構。 運行部署腳本後，需要在 Azure 門戶中執行一些手動設定，以將元件和服務連結在一起。

示例應用面向在 Azure 上開發應用程式，希望在其應用程式中實施安全措施的初學者。

在開發和部署此應用程式時，您將學習如何：

- 創建 Azure 金鑰保存庫實例，存儲和檢索其機密。
- 為 PostgreSQL 部署 Azure 資料庫，設置安全密碼並授權訪問它。
- 在 Azure Web 應用上為 Linux 運行阿爾卑斯 Linux 容器，並為 Azure 資源啟用託管標識。
- 使用使用[OWASP 前 10 個規則集](https://coreruleset.org/)的防火牆創建和配置 Azure 應用程式閘道實例。
- 使用 Azure 服務對傳輸中和靜態的資料進行加密。

開發和部署此應用後，您將設置以下示例 Web 應用以及描述的配置和安全措施。

![示例 Web 應用](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>架構

該應用程式是一個典型的n層應用程式，具有三層。 此處顯示了集成了監視和金鑰管理元件的前端、後端和資料庫層：

![應用程式架構](./media/secure-web-app/architecture.png)

該體系結構由以下元件組成：

- [Azure 應用程式閘道](../../application-gateway/index.yml)。 為我們的應用程式體系結構提供閘道和防火牆。
- [Linux 上的 Azure Web 應用程式](../../app-service/containers/app-service-linux-intro.md)。 提供在 Linux 環境中運行 Python 應用的容器運行時。
- [Azure 金鑰保存庫](../../key-vault/index.yml)。 存儲和加密應用的機密，並管理圍繞其創建訪問策略。
- [用於 PostgreSQL](https://azure.microsoft.com/services/postgresql/)的 Azure 資料庫 。 安全地存儲我們應用的資料。
- [Azure 安全中心和](../../security-center/index.yml) [Azure 應用程式見解](../../azure-monitor/app/app-insights-overview.md)。 提供有關應用程式操作的監視和警報。

## <a name="threat-model"></a>威脅模型

威脅建模是識別業務和應用程式的潛在安全威脅，然後確保制定適當的緩解計畫的過程。

此示例使用[Microsoft 威脅建模工具](threat-modeling-tool.md)為安全示例應用實現威脅建模。 通過繪製元件和資料流程圖，可以在開發過程的早期識別問題和威脅。 這樣可以節省時間和金錢。

這是示例應用的威脅模型：

![威脅模型](./media/secure-web-app/threat-model.png)

以下螢幕截圖中顯示了威脅建模工具生成的一些示例威脅和潛在漏洞。 威脅模型概述了暴露的攻擊面，並提示開發人員考慮如何緩解問題。

![威脅模型輸出](./media/secure-web-app/threat-model-output.png)

例如，通過清理使用者輸入和使用 Azure 資料庫中的存儲函數進行 PostgreSQL 來緩解上述威脅模型輸出中的 SQL 注入。 此緩解措施可防止在資料讀取和寫入期間任意執行查詢。

開發人員通過緩解威脅模型輸出中的每個威脅來提高系統的整體安全性。

## <a name="deployment"></a>部署

以下選項允許您在 Azure 應用服務上運行 Linux：

- 從 Azure 上已構建的預構建 Microsoft 容器清單中選擇一個容器，該容器已使用支援技術（Python、Ruby、PHP、JAVA、Node.js、.NET Core）創建。
- 使用自訂容器。 選擇您自己的容器註冊表作為映射的源，並基於支援 HTTP 的許多可用技術進行構建。

在此示例中，您將運行部署腳本，該腳本將 Webapp 部署到應用服務並創建資源。

該應用程式可以使用如下所示的不同部署模型：

![部署資料流程圖](./media/secure-web-app/deployment.png)

在 Azure 上部署應用的方法有很多種，包括：

- Azure 資源管理員範本
- PowerShell
- Azure CLI
- Azure 入口網站
- Azure DevOps

此應用程式使用：

- [Docker](https://docs.docker.com/)創建和生成容器映射。
- 用於部署的[Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Docker 集線器](https://hub.docker.com/)作為容器註冊表。

## <a name="security-considerations"></a>安全性考量

### <a name="network"></a>網路

示例應用對流入和流出網路的傳輸中資料使用端到端 SSL 加密。 閘道配置了自簽章憑證。
> [!IMPORTANT]
> 此演示使用自簽章憑證。 在生產環境中，應從經過驗證的憑證授權單位 （CA） 獲取證書。

當網路流量檢測到惡意流量時，應用程式防火牆還會檢查傳入流量並提醒管理員。
應用程式閘道可降低威脅模型中發現的 DDoS 和 SQL 注入威脅的可能性。

### <a name="identity"></a>身分識別

要登錄到門戶，示例應用對已分配對資源的存取權限的 Azure 活動目錄 （Azure AD） 管理員使用多重要素驗證。
示例應用使用託管標識從 Azure 金鑰保存庫獲取讀取和檢索機密的許可權，確保應用不需要硬編碼憑據和權杖即可讀取機密。 Azure AD 會自動創建應用在使用託管標識時需要讀取和修改機密的服務主體。

Azure 資源和 MFA 的託管標識使對手更難獲得特權並升級其在系統中的許可權。 威脅模型指出了這種威脅。
該應用程式使用 OAuth，它允許使用者在 OAuth 應用程式中註冊登錄到應用程式。

### <a name="storage"></a>存放裝置

PostgreSQL 資料庫中的資料由 PostgreSQL 的 Azure 資料庫自動加密靜態資料。 資料庫授權應用服務 IP 位址，以便只有已部署的應用服務 Web 應用才能使用正確的身份驗證憑據訪問資料庫資源。

### <a name="logging-and-auditing"></a>記錄與稽核

應用通過使用應用程式見解來跟蹤發生的指標、日誌和異常，從而實現日誌記錄。 此日誌記錄提供了足夠的應用中繼資料，可通知開發人員和操作團隊成員有關應用的狀態。 它還提供了足夠的資料，以便在發生安全事件時回溯。

## <a name="cost-considerations"></a>成本考量

如果還沒有 Azure 帳戶，則可以創建一個免費帳戶。 轉到[免費帳戶頁面](https://azure.microsoft.com/free/)即可開始，瞭解可以使用免費 Azure 帳戶做什麼，並瞭解哪些產品在 12 個月內是免費的。

要使用安全功能部署示例應用中的資源，您需要為某些高級功能付費。 隨著應用的擴展以及 Azure 提供的免費層和試用版需要升級以滿足應用程式要求，您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)估計成本。

## <a name="deploy-the-solution"></a>部署解決方案

### <a name="prerequisites"></a>Prerequisites

要啟動和運行應用程式，您需要安裝這些工具：

- 用於修改和查看應用程式代碼的代碼編輯器。[視覺化工作室代碼](https://code.visualstudio.com/)是一個開源選項。
- [開發電腦上的 Azure CLI。](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)在你的系統上。 Git 用於在本地克隆原始程式碼。
- [jq，](https://stedolan.github.io/jq/)一種以方便使用方式查詢JSON的UNIX工具。

您需要 Azure 訂閱來部署示例應用的資源。 如果沒有 Azure 訂閱，則可以[創建一個免費帳戶](https://azure.microsoft.com/free/)來測試示例應用。

安裝這些工具後，即可在 Azure 上部署應用。

### <a name="environment-setup"></a>環境設定

運行部署腳本以設置環境和訂閱：

1. 要克隆原始程式碼存儲庫，請使用此 Git 命令：

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. 要進入目錄，請使用以下命令：

   ```shell
   cd tutorial-project/scripts
   ```

3. 腳本資料夾中有特定于您正在使用的平臺（Windows 或 Linux）的檔。 安裝 Azure CLI 後，請通過運行此 Azure CLI 命令在命令提示符下登錄到 Azure 帳戶：

   ```azurecli-interactive
   az login
   ```

瀏覽器將打開，使用您的憑據登錄。 登錄後，可以從命令提示符開始部署資源。

部署腳本`deploy-powershell.ps1`並`deploy-bash.sh`包含部署整個應用程式的代碼。
要部署解決方案：

1. 如果在 PowerShell 上運行`deploy-powershell.ps1`檔，則鍵入`./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME`將區域和資源組名稱替換為合適的 Azure 區域和資源組的名稱
2. 如果您在 Linux 上通過鍵入`deploy-bash.sh``/deploy-bash.sh REGION RESOURCE_GROUP_NAME`來運行該檔，則可能需要通過鍵入使檔可執行`chmod +x deploy-bash.sh`

以下示例演示了關鍵元件的程式碼片段。 您可以通過運行部署檔單獨部署示例，也可以與元件的其餘部分一起部署這些示例。

### <a name="implementation-guidance"></a>實作指引

部署腳本是一個腳本，可以分解為四個階段。 每個階段部署和配置[體系結構關係圖](#architecture)中的 Azure 資源。

四個階段是：

- 部署 Azure 金鑰保存庫。
- 為 PostgreSQL 部署 Azure 資料庫。
- 在 Linux 上部署 Azure Web 應用。
- 使用 Web 應用程式防火牆部署應用程式閘道。

每個階段都使用以前部署的資源中的配置在前一個階段構建。

要完成實施步驟，請確保已安裝"[先決條件](#prerequisites)"下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure 金鑰保存庫

在本節中，創建和部署用於存儲機密和證書的 Azure 金鑰保存庫實例。

完成部署後，在 Azure 上部署了 Azure 金鑰保存庫實例。

要使用 Azure CLI 部署 Azure 金鑰保存庫，請使用：

1. 聲明 Azure 金鑰保存庫的變數。
2. 註冊 Azure 金鑰保存庫提供程式。
3. 為實例創建資源組。
4. 在步驟 3 中創建的資源組中創建 Azure 金鑰保存庫實例。

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

最佳做法是在使用金鑰保存庫訪問資源的應用中對 Azure 資源使用託管標識。 當金鑰保存庫的訪問金鑰未存儲在代碼或配置中時，您的安全狀態會提高。

#### <a name="deploy-azure-database-for-postgresql"></a>為 PostgreSQL 部署 Azure 資料庫

PostgreSQL 的 Azure 資料庫的工作方式如下，首先創建資料庫伺服器，然後創建用於存儲架構和資料的資料庫。

完成部署後，在 Azure 上運行 PostgreSQL 伺服器和資料庫。

要使用 Azure CLI 部署後格雷SQL的 Azure 資料庫，請使用：

1. 使用 Azure CLI 和 Azure 訂閱設置打開終端。
2. 生成用於訪問資料庫的安全使用者名和密碼組合。 （這些應存儲在 Azure 金鑰保存庫中，用於使用它們的應用。
3. 創建 PostgreSQL 伺服器實例。
4. 在步驟 3 中創建的伺服器實例上創建資料庫。
5. 在 PostgreSQL 實例上運行 PostgreSQL 腳本。

以下代碼依賴于上面部署 KeyVault 步驟中存儲在 Azure KeyVault 中的 PGUSERNAME 和 PGPASSWORD 機密。

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

部署資料庫後，需要將其憑據和連接字串存儲在 Azure 金鑰保存庫中。
在腳本資料夾中，有一個`functions.sql`檔包含 PL/pgSQL 代碼，在運行時創建存儲的函數。 運行此檔參數化輸入以限制 SQL 注入。

PostgreSQL 與一個稱為用於`psql`連接到資料庫的工具捆綁在一起。 要運行`functions.sql`，需要從本地電腦連接到 PostgreSQL 實例的 Azure 資料庫，並從那裡運行它。 psql 工具的安裝包含在每個作業系統 PostgreSQL 的預設安裝中。
有關詳細資訊，請參閱[psql 文檔](https://www.postgresql.org/docs/9.3/app-psql.html)。

Azure 雲外殼還包括該工具`psql`。 您可以通過選擇雲外殼圖示直接從 Azure 門戶使用雲外殼。

要啟用對 PostgreSQL 實例的遠端存取，您需要在 PostgreSQL 中授權 IP 位址。
通過訪問**連接安全**選項卡、選擇 **"添加用戶端 IP**"以及保存新設置來啟用此存取權限。

![授權用戶端 IP](./media/secure-web-app/add-client-ip-postgres.png)

如果使用雲外殼而不是本地 psql 工具，請選擇"**允許訪問 Azure 服務**"並將其值更改為**ON**以允許雲外殼訪問。

然後，通過運行以下 psql 命令與 Azure 門戶上的 PostgreSQL 實例**的連接字串**選項卡的連接字串參數連接到實例。
將空大括弧替換為資料庫的連接字串邊欄選項卡中的參數，用 Azure 金鑰保存庫的密碼替換密碼。

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

在確保已連接到資料庫後，請運行以下 PL/pgSQL 腳本。 該腳本創建用於將資料插入資料庫的存儲函數。

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

有關如何為 PostgreSQL 設置 SSL 和憑證授權單位 （CA） 驗證的詳細資訊，請參閱[為 PostgreSQL 在 Azure 資料庫中配置 SSL 連線](/azure/postgresql/concepts-ssl-connection-security)。

容器中包含根憑證。 獲取證書的步驟包括：

1. 從[憑證授權單位](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下載證書檔。
2. [在您的電腦上下載並安裝 OpenSSL。](/azure/postgresql/concepts-ssl-connection-security)
3. 解碼證書檔：

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

在此處配置[SSL 連線安全性](/azure/postgresql/concepts-ssl-connection-security)，請在此處閱讀有關如何為 PostgreSQL 配置 SSL 安全性的更多內容。

#### <a name="deploy-azure-web-apps-on-linux"></a>在 Linux 上部署 Azure Web 應用

在 Azure 應用服務的基礎上輕鬆構建 Linux 服務，因為 Azure 為廣泛使用的語言（如 Python、Ruby、C# 和 JAVA）提供了一組預構建的容器和映射。 Azure 還支援自訂容器，它允許在 Azure 應用服務平臺上運行幾乎所有程式設計語言。

正在部署的應用程式是一個簡單的Python應用程式，運行在最新的UbuntuLinux發行版本。 它分別連接到在前幾節中為憑據管理和資料存儲創建的 Azure 金鑰保存庫和 PostgreSQL 實例。

以下 Docker 檔在應用程式的根資料夾中提供：

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

上面的 Dockerfile 用於生成託管在 Azure 容器註冊表上的`mcr.microsoft.com/samples/basic-linux-app`容器。

以下代碼：

1. 聲明應用服務實例的變數和名稱。
2. 為應用服務方案創建資源組。
3. 在 Linux 容器實例上設置 Azure Web 應用。
4. 啟用 Web 應用容器的日誌記錄。
5. 在容器的應用設置中設置一些應用配置。

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

此腳本為應用服務實例創建分配的身份，該標識可與 MSI 一起使用，與 Azure 金鑰保存庫進行交互，而不會在代碼或配置中使用硬編碼機密。

轉到門戶中的 Azure 金鑰保存庫實例，以授權訪問策略選項卡上分配的標識。選擇 **"添加新訪問策略**"。 在**Select 主體**下，搜索與創建的應用服務實例的名稱類似的應用程式名稱。
附加到應用程式的服務主體應可見。 選擇它並保存訪問策略頁，如下圖所示。

由於應用程式只需要檢索金鑰，因此在機密選項中選擇 **"獲取**"許可權，允許訪問，同時減少授予的許可權。

![Key Vault 存取原則](./media/secure-web-app/kv-access-policy.png)

*創建金鑰保存庫訪問策略*

保存訪問策略，然後在 **"訪問策略"** 選項卡上保存新更改以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>在啟用了 Web 應用程式防火牆後部署應用程式閘道

在 Web 應用中，不建議您在互聯網上直接向外部世界公開服務。
負載平衡和防火牆規則為傳入流量提供了更多的安全性和控制，並説明您對其進行管理。

要部署應用程式閘道實例：

1. 創建資源組以容納應用程式閘道。
2. 預配虛擬網路以附加到閘道。
3. 為虛擬網路中的閘道創建子網。
4. 預配公共 IP 位址。
5. 預配應用程式閘道。
6. 在閘道上啟用 Web 應用程式防火牆。

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

前面的腳本：

1. 在 Azure 上創建新的自簽章憑證。
2. 將自簽章憑證下載為 base64 編碼檔。
3. 為自簽章憑證生成密碼。
4. 將證書匯出為使用密碼簽名的 PFX 檔。
5. 將證書的密碼存儲在 Azure 金鑰保存庫中。

本節部署應用程式閘道：

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

完成部署後，您有一個啟用了 Web 應用程式防火牆的應用程式閘道。

閘道實例公開 HTTPS 埠 443。 此配置可確保我們的應用只能通過 HTTPS 在埠 443 上訪問。

阻止未使用的埠和限制攻擊面暴露是一種安全最佳做法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>將網路安全性群組添加到應用服務實例

應用服務實例可以與虛擬網路集成。 此集成允許使用管理應用傳入和傳出流量的網路安全性群組策略配置它們。

1. 要啟用此功能，請在 Azure 應用服務實例邊欄選項卡上，**在"設置"** 下選擇 **"網路**"。 在右側窗格中，在**VNet 集成**下，選擇 **"按一下此處配置**"。

   ![新的虛擬網路集成](./media/secure-web-app/app-vnet-menu.png)

    *應用服務的新虛擬網路集成*

1. 在下一頁上，選擇 **"添加 VNET（預覽）"。**

1. 在下一個功能表上，選擇在以`hello-vnet`開始的部署中創建的虛擬網路。 您可以創建新子網或選擇現有子網。
   在這種情況下，創建一個新的子網。 將**位址範圍**設置為**10.0.3.0/24，** 並命名子**網應用子網**。

   ![應用服務虛擬網路配置](./media/secure-web-app/app-vnet-config.png)

    *應用服務的虛擬網路配置*

現在，您已經啟用了虛擬網路集成，您可以將網路安全性群組添加到我們的應用。

1. 使用搜索框，搜索**網路安全性群組**。 在結果中選擇**網路安全性群組**。

    ![搜索網路安全性群組](./media/secure-web-app/nsg-search-menu.png)

    *搜索網路安全性群組*

2. 在下一個功能表上，選擇 **"添加**"。 輸入應位於的 NSG**和資源組****的名稱**。 此 NSG 將應用於應用程式閘道的子網。

    ![創建 NSG](./media/secure-web-app/nsg-create-new.png)

    *創建 NSG*

3. 創建 NSG 後，選擇它。 在其邊欄選項卡中，在 **"設置"** 下，選擇 **"入站安全規則**"。 配置這些設置以允許通過埠 443 進入應用程式閘道的連接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在閘道 NSG 的出站規則中，通過創建以服務標記`AppService`為目標的規則，添加允許出站連接到應用服務實例的規則：

   ![添加 NSG 的出站規則](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *添加 NSG 的出站規則*

    添加另一個出站規則，允許閘道將出站規則發送到虛擬網路。

   ![添加其他出站規則](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加其他出站規則*

5. 在 NSG 的子網邊欄選項卡上，選擇 **"關聯**"，選擇在部署中創建的虛擬網路，然後選擇名為**gw-子網的**閘道子網 。 NSG 應用於子網。

6. 創建另一個 NSG，如前面的步驟中，此時為應用服務實例。 給它一個名字。 添加埠 443 的入站規則，就像對應用程式閘道 NSG 所做的那樣。

   如果在應用服務環境實例上部署了應用服務實例（此應用不是這種情況，則可以通過打開應用服務 NSG 的入站安全性群組上的埠 454-455 來添加入站規則以允許 Azure 服務運行狀況探測。 配置：

   ![添加 Azure 服務運行狀況探測的規則](./media/secure-web-app/nsg-create-healthprobes.png)

    *添加 Azure 服務運行狀況探測的規則（僅限應用服務環境）*

7. 在出站安全規則中，創建一個新的出站安全規則，允許應用服務實例與 PostgreSQL 資料庫進行通信。 像這樣配置它：

   ![允許出站後 SQL 連接的規則](./media/secure-web-app/nsg-outbound-postgresql.png)

   *添加規則以允許出站 PostgreSQL 連接*

要限制攻擊面，請修改應用服務網路設置，以僅允許應用程式閘道訪問應用程式。
通過進入應用服務網路選項卡、選擇 **"IP 限制"** 選項卡以及創建只允許應用程式閘道的 IP 直接存取服務的允許規則來執行此操作。

可以從閘道的概述頁檢索閘道的 IP 位址。 在**IP 位址 CIDR**選項卡上，輸入此格式的`<GATEWAY_IP_ADDRESS>/32`IP 位址： 。

![僅允許閘道](./media/secure-web-app/app-allow-gw-only.png)

*僅允許閘道 IP 訪問應用服務*

#### <a name="implement-azure-active-directory-oauth"></a>實現 Azure 活動目錄 OAuth

在示例 Web 應用頁上分發的 Azure 文檔是我們應用中可能需要保護的資源。 可以使用 Azure 活動目錄 （Azure AD） 使用不同的身份驗證流實現 Web、桌面和移動應用的身份驗證。
該應用程式使用**與 Microsoft 登錄**，允許應用讀取已添加到單租戶 Azure AD 使用者清單中的使用者的設定檔。

在 Azure 門戶中，將應用配置為使用所需的憑據：

1. 選擇**Azure 活動目錄**，或使用搜索框搜索它。

2. 選擇**新註冊**：

   ![創建註冊](./media/secure-web-app/ad-auth-create.png)

   *創建 Azure AD 應用註冊*

3. 在下一頁上，輸入應用名稱。 在 **"支援帳戶類型**"下，**選擇"僅在此組織目錄中的帳戶**"。
    在**重定向 URI**下，輸入應用將運行的基本域加上帶有權杖終結點的一個域。 例如 *：GATEWAY_HASH*.cloudapp.net/token。

   ![配置 Azure AD 應用註冊](./media/secure-web-app/ad-auth-type.png)

   *配置 Azure AD 應用註冊*

4. 將顯示顯示已註冊應用及其資訊的螢幕。 您需要將此資訊添加到 Azure 金鑰保存庫實例中。
   1. 複製應用程式（用戶端）ID 並將其保存在金鑰保存庫中為`CLIENTID`。
   2. 複製您在上一步中輸入的重定向 URI 並將其另存為`REDIRECTURI`。
   3. 複製 Azure AD 預設目錄名稱（其格式*名稱*.microsoftonline.com，並將其保存在金鑰保存庫中為`TENANT`。
   4. 轉到以前創建的 Azure AD 應用的 **"證書&機密**"選項卡，然後選擇 **"新建用戶端機密**"，如下圖所示。 設置到期日期，然後複製生成的值並將其保存在金鑰保存庫中為`CLIENTSECRET`。

      ![Azure AD 授權金鑰](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 授權金鑰*

   5. 使用任何命令列/連線工具生成安全的隨機金鑰。 將其另存到金鑰保管`FLASKSECRETKEY`庫中。 應用程式框架使用此金鑰創建會話。
        要瞭解如何生成金鑰，請參閱["跳蚤會話](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)"。

5. 配置登錄後，需要將使用者添加到 Azure AD 連結，以允許他們登錄到資源。 要添加它們，請轉到 Azure AD 中的 **"使用者"** 選項卡，選擇 **"所有使用者**"，然後選擇 **"新使用者**"或 **"新來賓使用者**"。 對於測試，您可以添加來賓使用者並邀請使用者進入目錄。 或者，如果應用正在運行的域已過驗證，則可以添加新使用者。 在此示例中，只能註冊在 Azure AD 租戶中註冊的使用者才能進行訪問。 有關多租戶登錄訪問的資訊，請參閱文檔。

   ![將使用者添加到預設域](./media/secure-web-app/ad-auth-add-user.png)

   *將使用者添加到預設的 Azure 活動目錄域*

將 Azure AD 配置和機密添加到金鑰保存庫後，可以使用 Azure OAuth 身份驗證將使用者身份驗證到應用中。
在應用代碼中，這由 Azure 活動目錄身份驗證庫 （ADAL） 處理。

在金鑰保存庫中包含機密並應用程式可以訪問機密和資料庫後，可以通過閘道的應用程式 URL （，https://GATEWAY_HASH.cloudapp.net)可以從其邊欄選項卡獲取）訪問應用程式服務。

如果在登錄到 Azure AD 時，會收到一個錯誤，指出"使用者未在嘗試登錄的目錄中註冊"，則需要添加該使用者。 要添加使用者，請轉到 Azure AD 的 **"使用者"** 選項卡，並通過輸入使用者的詳細資訊手動添加使用者，或者通過在 **"邀請來賓"** 邊欄選項卡中輸入以來賓使用者身份的使用者電子郵件地址來邀請使用者加入 Azure AD。

#### <a name="deploy-application-insights"></a>部署 Application Insights
現在，應用已部署並工作，您需要處理應用中發生的錯誤以及日誌記錄和跟蹤資料收集。
日誌記錄和跟蹤資料收集提供了對應用中發生的審核事件的視圖。

應用程式見解是收集使用者或系統可以生成的日誌的服務。

要創建應用程式見解實例，

1. 使用 Azure 門戶中的搜索框搜索**應用程式見解**。
2. 選取 [Application Insights] ****。 提供此處顯示的詳細資訊以創建實例。

   ![創建應用程式見解實例](./media/secure-web-app/app-insights-data.png)

部署完成後，您有一個應用程式見解實例。

創建應用程式見解實例後，需要使應用瞭解允許其向雲發送日誌的檢測金鑰。 為此，請檢索應用程式見解金鑰，並在 Azure 為應用程式見解提供的應用程式庫中使用它。 最佳做法是在 Azure 金鑰保存庫中存儲金鑰和機密，以確保它們的安全。

對於基本示例應用，在創建應用程式見解實例後，需要使應用瞭解允許其向雲發送日誌的檢測金鑰。
在金鑰保存庫中，設置`APPINSIGHTSKEY`機密並將其值設置為檢測金鑰。 這樣做允許應用向應用程式見解發送日誌和指標。

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>實現 Azure 活動目錄的多重要素驗證

管理員需要確保門戶中的訂閱帳戶受到保護。 訂閱容易受到攻擊，因為它管理您創建的資源。 為了保護訂閱，請在訂閱的**Azure 活動目錄**選項卡上啟用多重要素驗證。

Azure AD 基於應用於符合特定條件的使用者或使用者組的策略進行操作。
Azure 創建一個預設策略，指定管理員需要雙重身份驗證才能登錄到門戶。
啟用此策略後，系統可能會提示您登出並重新登錄到 Azure 門戶。

要為管理員登錄啟用 MFA：：

1. 轉到 Azure 門戶中的**Azure 活動目錄**選項卡
2. 在安全類別下，選擇條件訪問。 您會看到這個畫面：

   ![條件訪問 - 策略](./media/secure-web-app/ad-mfa-conditional-add.png)

如果無法創建新策略：

1. 轉到**MFA**選項卡。
2. 選擇 Azure AD 高級**免費試用版**連結以訂閱免費試用版。

   ![Azure AD 高級免費試用版](./media/secure-web-app/ad-trial-premium.png)

返回到條件訪問螢幕。

1. 選擇新的策略選項卡。
2. 輸入原則名稱。
3. 選擇要為其啟用 MFA 的使用者或組。
4. 在 **"訪問"控制項**下，選擇"**授予**"選項卡，然後選擇 **"需要多重要素驗證**"（如果需要，則選擇其他設置）。

   ![需要 MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

您可以通過選擇螢幕頂部的核取方塊來啟用策略，也可以在 **"條件訪問**"選項卡上啟用策略。啟用策略後，使用者需要 MFA 才能登錄到門戶。

有一個基線策略，它要求所有 Azure 管理員使用 MFA。 您可以立即在門戶中啟用它。 啟用此策略可能會使當前會話無效，並迫使您再次登錄。

如果未啟用基線策略：

1. 為**管理員選擇"需要 MFA"。**
2. **選擇立即使用策略**。

   ![立即選擇使用策略](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure 哨兵監視應用和資源

隨著應用程式的增長，很難聚合從資源接收的所有安全信號和指標，並使它們以面向操作的方式有用。

Azure Sentinel 旨在收集資料、檢測可能的威脅類型，並提供安全事件的可見度。
在等待手動干預時，Azure Sentinel 可以依賴預先編寫的行動手冊來啟動警報和事件管理過程。

示例應用由 Azure Sentinel 可以監視的多個資源組成。
要設置 Azure Sentinel，首先需要創建日誌分析工作區，該工作區存儲從各種資源收集的所有資料。

要創建此工作區，

1. 在 Azure 門戶中的搜索框中，搜索**日誌分析**。 選取 [Log Analytics 工作區]****。

   ![搜索日誌分析工作區](./media/secure-web-app/sentinel-log-analytics.png)

    *搜索日誌分析工作區*

2. 在下一頁上，選擇 **"添加**"，然後為工作區提供名稱、資源組和位置。
   ![建立 Log Analytics 工作區](./media/secure-web-app/sentinel-log-analytics-create.png)

   *創建日誌分析工作區*

3. 使用搜索框搜索 Azure**哨兵**。

   ![搜尋 Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *搜索 Azure 哨兵*

4. 選擇 **"添加**"，然後選擇之前創建的日誌分析工作區。

   ![添加日誌分析工作區](./media/secure-web-app/sentinel-workspace-add.png)

    *添加日誌分析工作區*

5. 在**Azure 哨兵 - 資料連線器**頁上，在**配置**下，選擇 **"資料連線器**"。 您將看到一系列 Azure 服務，可以連結到 Azure Sentinel 中的日誌分析存儲實例進行分析。

   ![日誌分析資料連接器](./media/secure-web-app/sentinel-connectors.png)

    *向 Azure 哨兵添加資料連線器*

   例如，要連接應用程式閘道，請執行以下步驟：

   1. 打開 Azure 應用程式閘道實例邊欄選項卡。
   2. 在 [監視]**** 下方，選取 [診斷設定]****。
   3. 選擇 **"添加診斷設置**"。

      ![添加應用程式閘道診斷](./media/secure-web-app/sentinel-gateway-connector.png)

      *添加應用程式閘道診斷*

   4. 在 **"診斷設置"** 頁上，選擇您創建的日誌分析工作區，然後選擇要收集的所有指標並將其發送到 Azure Sentinel。 選取 [儲存]****。

        ![Azure 哨兵連接器設置](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure 哨兵連接器設置*

  來自資源的指標位於 Azure Sentinel 中，您可以在其中查詢和調查它們。

   在 Azure 金鑰保存庫、公共 IP 位址、PostgreSQL Azure 資料庫以及帳戶中支援診斷日誌的任何服務的診斷設置中添加相同的指標。

設置指標後，Azure Sentinel 具有要分析的資料。

## <a name="evaluate-and-verify"></a>評估和驗證

開發和部署體系結構後，需要確保代碼和部署的服務符合安全標準。 以下是驗證軟體的一些步驟：

- 靜態程式碼分析
- 弱點掃描
- 查找和修復應用程式依賴項中的漏洞

這些是安全開發最佳實踐的基本構建基塊。

### <a name="static-code-analysis"></a>靜態程式碼分析

對於示例應用，使用靜態分析工具進行驗證涉及使用污點檢查和資料流程分析等技術查找應用代碼中的漏洞。 Python 靜態分析工具讓您更有信心你的應用是安全的。

**進行 Lint 檢查**

PyFlakes 是 Python 林丁庫，可説明您從應用中刪除死代碼和未使用的函數，如下所示：

![皮片](./media/secure-web-app/pyflakes.png)

林亭提供了提示和可能的更改，這些提示和可能更改可以使代碼更簡潔，在運行時不易出錯。

**PyLint**

PyLint 為這個專案提供了最大的價值。 它執行代碼標準檢查、錯誤檢查和重構提示，以確保在伺服器上運行的代碼是安全的。 通過使用 PyLint 更新代碼，您可以消除錯誤並改進 PyLint 評級，如下圖顯示。

![皮林特之前](./media/secure-web-app/before-pylint.png)

*皮林特之前*

修復林廷工具發現的某些代碼錯誤後，您更有信心代碼不容易出錯。 修復錯誤可顯著降低將代碼部署到生產環境時可能出現的安全風險。

![皮林特之後](./media/secure-web-app/after-pylint.png)

*皮林特之後*

### <a name="vulnerability-scanning"></a>弱點掃描

[OWASP 的 ZAP](https://www.zaproxy.org/)工具是一個開源 Web 應用程式漏洞掃描程式，可用於檢查示例應用是否存在漏洞。 在示例應用上運行該工具會顯示一些可能的錯誤和攻擊媒介。

![ZAP 工具](./media/secure-web-app/zap-tool.png)

*ZAP 工具*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>查找和修復應用依賴項中的漏洞

要查找和修復應用程式依賴項，可以使用[OWASP 的依賴項檢查](https://www.owasp.org/index.php/OWASP_Dependency_Check)。

安全性是檢查依賴項的類似應用程式。 你可以在[GitHub](https://github.com/pyupio/safety)上找到它。 安全掃描在已知漏洞資料庫中發現的漏洞。

![安全性](./media/secure-web-app/pysafety.png)

*安全*

## <a name="next-steps"></a>後續步驟

以下文章可説明您設計、開發和部署安全應用程式。

- [設計](secure-design.md)
- [發展](secure-develop.md)
- [部署](secure-deploy.md)
