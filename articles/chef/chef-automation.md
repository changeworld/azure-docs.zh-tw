---
title: 快速入門 - 使用 Chef 在 Azure 中設定 Windows 虛擬機器
description: 在本快速入門中，您會了解如何使用 Chef 在 Azure 中部署和設定 Windows 虛擬機器
keywords: chef, azure, devops, 虛擬機器
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589492"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>快速入門 - 使用 Chef 在 Azure 中設定 Windows 虛擬機器

Chef 可讓您傳遞自動化和所需的狀態組態。

在我們最新的雲端 API 版本中，Chef 提供與 Azure 的緊密整合，您可以透過單一命令佈建及部署組態狀態。

在本文中，您將設定可佈建 Azure 虛擬機器的 Chef 環境，並會逐步指導您建立原則或逐步指南，然後將此逐步指南部署到 Azure 虛擬機器中。

## <a name="chef-basics"></a>Chef 基本概念

開始閱讀本文之前，請[檢閱 Chef 的基本概念](https://www.chef.io/chef)。

下圖顯示高層級的 Chef 架構。

![Chef 架構](media/chef-automation/chef-architecure.png)

Chef 有三個主要的架構元件： 
- Chef 伺服器 - 是管理的重點且包含兩個選項：代管解決方案或內部部署解決方案。
- Chef 用戶端 (節點) - 是位於您所管理之伺服器上的代理程式。
- Chef 工作站 - 是您建立原則和執行管理命令的管理工作站以及 Chef 工具軟體套件的名稱。

一般而言，您會看到**您的工作站**作為執行命令的位置，**Chef 工作站**作為軟體套件。

例如，您將下載 knife 命令作為 **Chef 工作站**的一部分，但是您從**工作站**執行 knife 命令來管理基礎結構。

Chef 也使用「逐步指南」  和「配方」  的概念。 這些字詞分別是定義和套用至伺服器的原則。

## <a name="preparing-your-workstation"></a>準備工作站

首先，藉由建立可儲存 Chef 設定檔和 cookbook 的目錄來準備您的工作站。

建立名為 `C:\Chef` 的目錄。

將最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 版本下載並安裝到您的工作站。

## <a name="configure-azure-service-principal"></a>設定 Azure 服務主體

我們將使用服務主體，協助我們從 Chef 工作站建立 Azure 資源。  若要建立具有必要權限的相關服務主體，請在 PowerShell 中執行下列命令：
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

記下您的 SubscriptionID、TenantID、ClientID 和用戶端密碼 (先前在本教學課程中設定的密碼)，因為您需要這些值。 

## <a name="setup-chef-server"></a>設定 Chef 伺服器 URL

本指南假設您會註冊已裝載的 Chef。

如果您尚未使用 Chef 伺服器，則可以：

* 註冊[受控 Chef](https://manage.chef.io/signup)，這是開始使用 Chef 的最快方式。
* 按照 [Chef Docs](https://docs.chef.io/) 中的[安裝說明](https://docs.chef.io/install_server.html)，在 Linux 型電腦上安裝獨立的 Chef 伺服器。

### <a name="creating-a-hosted-chef-account"></a>建立受控 Chef 帳戶

在[這裡](https://manage.chef.io/signup)註冊受控 Chef 帳戶。

在註冊過程中，我們將要求您建立新的組織。

![建立組織視窗](media/chef-automation/create-organization.png)

建立組織後，請下載「入門套件」。

![設定 Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> 如果您收到提示，警告您將重新設定金鑰，您可以繼續作業，因為我們尚未設定任何基礎結構。
>

此入門套件 zip 檔案包含 `.chef` 目錄中您的組織組態檔和使用者金鑰。

請分開下載 `organization-validator.pem`，因為它是私密金鑰，不應該儲存在 Chef 伺服器上。 前往 [Chef 管理](https://manage.chef.io/)中的 [管理] 區段並選取 [重設驗證金鑰]，其會提供可供您個別下載的檔案。 將檔案儲存至 c:\chef。

### <a name="configuring-your-chef-workstation"></a>設定 Chef 工作站

將 `chef-starter.zip` 的內容解壓縮至 `c:\chef`。

將 `chef-starter\chef-repo\.chef` 下的所有檔案複製到您的 `c:\chef` 目錄。

如果 `organization-validator.pem` 檔案儲存在 `c:\Downloads`中，請將它複製到 `c:\chef`。

您的目錄現在看起來應該類似以下範例。

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

您現在應該在 c:\chef 的根目錄中有五個檔案和四個目錄 (包括空的 chef-repo 目錄)。

### <a name="edit-kniferb"></a>編輯 knife.rb

PEM 檔案包含可進行通訊的組織和管理員私密金鑰，而 knife.rb檔案則包含 knife 組態。 我們將需要編輯 knife.rb 檔案。

在您選擇的編輯器中開啟 knife.rb 檔案。 未變更的檔案應如下所示：

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

將下列資訊新增至 knife.rb 中，並以您的資訊取代預留位置：

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

這些行資料會確保 Knife 會參考 `c:\chef\cookbooks` 下的逐步指南目錄。

現在您的 `knife.rb` 檔案看起來應該會類似下列範例：

![Knife 檔案範例](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>安裝 Chef 工作站

接下來，[下載並安裝 Chef 工作站](https://downloads.chef.io/chef-workstation/)。

在預設位置中安裝 Chef 工作站。

在桌面上，您會看到 CW PowerShell。 此工具可用來與 Chef 產品互動。 CW PowerShell 提供了新的命令，例如 `chef-run` 以及 Chef CLI 命令 (例如 `chef`)。 使用 `chef -v` 查看已安裝的 Chef 工作站版本和 Chef 工具。 您還可以從 Chef 工作站應用程式中選取 [關於 Chef 工作站]  ，以檢查工作站版本。

`chef --version` 應該會傳回類似下列內容：

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> 路徑的順序很重要！ 如果您的 opscode 路徑順序不正確，則會出現問題。
>

在繼續之前，請重新啟動您的工作站。

### <a name="install-knife-azure"></a>安裝 Knife Azure

本教學課程假設您使用 Azure Resource Manager 來與您的虛擬機器互動。

安裝包含 Azure 外掛程式的 Knife Azure 擴充功能。

執行下列命令。

    chef gem install knife-azure ––pre

> [!NOTE]
> `–-pre` 引數可確保您會收到最新的 RC 版本 Knife Azure 外掛程式，該版本可讓您存取最新的 API 組合。
>
>

同時也可能安裝多個相依性。

![安裝 knife-azure 的輸出](./media/chef-automation/install-knife-azure.png)

若要確保一切都已正確設定，請執行下列命令。

    knife azurerm server list

如果一切都已正確設定，您會在捲動時看到可用的 Azure 映像清單。

恭喜！ 您的工作站已設定！

## <a name="creating-a-cookbook"></a>建立逐步指南

Chef 會使用逐步指南來定義一組您想在受控用戶端上執行的命令。 建立逐步指南非常簡單，只要使用 `chef generate cookbook` 命令來產生逐步指南範本。 此 cookbook 適用於自動部署 IIS Web 伺服器。

在 `C:\Chef directory` 下執行以下命令。

    chef generate cookbook webserver

此命令會在 C:\Chef\cookbooks\webserver 目錄下產生一組檔案。 接下來，定義一組需要 Chef 用戶端在受控虛擬機器上執行的命令。

這些命令會儲存在 default.rb. 在這個檔案中，請定義一組用來安裝 IIS、啟動 IIS 並將範本檔案複製到 `wwwroot` 資料夾的命令。

修改 C:\chef\cookbooks\webserver\recipes\default.rb 檔並加入下列幾行程式碼。

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

完成後，請儲存檔案。

## <a name="creating-a-template"></a>建立範本

在此步驟中，您將產生作為 `default.html` 頁面的範本檔案。

執行下列命令以產生範本：

    chef generate template webserver Default.htm

瀏覽至 `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` 檔案。 加入一些簡單的 *Hello World* HTML 程式碼來編輯檔案，然後儲存檔案。

## <a name="upload-the-cookbook-to-the-chef-server"></a>將逐步指南上傳到 Chef 伺服器

在此步驟中，您會將在本機電腦上建立的逐步指南複本，上傳到 Chef 代管伺服器。 上傳後，逐步指南便會出現在 [原則]  索引標籤底下。

    knife cookbook upload webserver

![將逐步指南安裝到 Chef 伺服器的結果](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>使用 Knife Azure 部署虛擬機器

部署 Azure 虛擬機器，並使用 `knife` 命令來套用 `Webserver` 逐步指南。

`knife` 命令也會安裝 IIS Web 服務和預設網頁。

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

`knife` 命令範例會在美國西部區域中，建立已安裝 Windows Server 2016 的「Standard_DS2_v2」  虛擬機器。 根據您的應用程式需求修改這些值。

執行命令後，瀏覽到 Azure 入口網站，查看您的機器開始佈建。

![正在佈建虛擬機器](./media/chef-automation/virtual-machine-being-provisioned.png)

命令提示字元會顯示下一步。

![建立虛擬機器時的 Knife 輸出](./media/chef-automation/knife-output-when-creating-vm.png)

部署完成後，就會顯示新虛擬機器的公用 IP 位址。 將此值貼到網頁瀏覽器中以檢視新的網站。 部署虛擬機器時，我們開啟了連接埠 80 以便在外部使用。   

![測試虛擬機器](./media/chef-automation/testing-the-virtual-machine.png)

此範例會使用有創意的 HTML 程式碼。

您也可以檢視節點狀態 [Chef 管理](https://manage.chef.io/)。 

![檢視節點狀態](./media/chef-automation/viewing-node-status.png)

別忘了您也可以透過連接埠 3389，從 Azure 入口網站的 RDP 工作階段進行連線。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Azure 上的 Chef](/azure/chef/)