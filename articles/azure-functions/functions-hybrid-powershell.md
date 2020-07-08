---
title: 使用 PowerShell 函數管理遠端內部部署資源
description: 瞭解如何在 Azure 轉送中設定混合式連線，以將 PowerShell 函式應用程式連線到內部部署資源，然後用來從遠端系統管理內部部署資源。
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83122099"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>在 Azure Functions 中使用 PowerShell 管理混合式環境，並 App Service 混合式連線

Azure App Service 混合式連線] 功能可讓您存取其他網路中的資源。 您可以在[混合式連接](../app-service/app-service-hybrid-connections.md)檔中深入瞭解這項功能。 本文說明如何使用這項功能來執行以內部部署伺服器為目標的 PowerShell 函式。 接著，您可以使用此伺服器來管理內部部署環境中，來自 Azure PowerShell 函式的所有資源。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>設定內部部署伺服器進行 PowerShell 遠端處理

下列腳本會啟用 PowerShell 遠端，並建立新的防火牆規則和 WinRM HTTPs 接聽程式。 基於測試目的，會使用自我簽署憑證。 在生產環境中，我們建議您使用已簽署的憑證。

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>在入口網站中建立 PowerShell 函式應用程式

App Service 的混合式連線功能僅適用于基本、標準和隔離定價方案。 當您使用 PowerShell 建立函數應用程式時，請建立或選取其中一個方案。

1. 從 Azure 入口網站功能表或[首頁] 頁面，選取 [建立資源]。

1. 在 [新增] 頁面中，選取 [計算] > [函數應用程式]。

1. 在 [基本資訊] 頁面中，使用下表中指定的函式應用程式設定。

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **[資源群組](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | 要在其中建立函式應用程式的新資源群組名稱。 |
    | **函式應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元為 `a-z` (區分大小寫)、`0-9` 以及 `-`。  |
    |**Publish**| 程式碼 | 發佈程式碼檔案或 Docker 容器的選項。 |
    | **執行階段堆疊** | 慣用語言 | 選擇 [PowerShell Core]。 |
    |**版本**| 版本號碼 | 選擇已安裝的執行階段版本。  |
    |**區域**| 慣用區域 | 選擇與您接近的[區域](https://azure.microsoft.com/regions/)，或選擇與函式將會存取之其他服務接近的區域。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="建立函數應用程式-基本概念。" border="true":::

1. 選取 [下一步：裝載]。 在 [裝載] 頁面中輸入下列設定。

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **[儲存體帳戶](../storage/common/storage-account-create.md)** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 您也可以使用現有帳戶，條件是必須符合[儲存體帳戶需求](../azure-functions/functions-scale.md#storage-account-requirements)。 |
    |**作業系統**| 慣用的作業系統 | 系統會根據您的執行階段堆疊選項預先選取作業系統，但您可以視需要變更設定。 |
    | **[方案類型](../azure-functions/functions-scale.md)** | **App service 方案** | 選擇 [ **App service 方案**]。 在 App Service 方案中執行時，您必須管理[函式應用程式的調整](../azure-functions/functions-scale.md)。  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="建立函數應用程式裝載。" border="true":::

1. 選取 [下一步：監視]。 在 [掛接] 頁面中輸入下列設定。

    | 設定      | 建議的值  | 描述 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | 預設 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 展開此設定或選取 [新增]，即可變更 Application Insights 名稱或在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中依您希望儲存資料的地點，選擇不同的區域。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="建立函數應用程式-監視。" border="true":::

1. 選取 [檢閱 + 建立]，以檢閱應用程式組態選項。

1. 在 [檢閱 + 建立] 頁面中檢閱您的設定，然後選取 [建立] 來佈建和部署函式應用程式。

1. 選取入口網站右上角的 [通知] 圖示，查看是否有 [部署成功] 訊息。

1. 選取 [前往資源]，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>建立函數應用程式的混合式連線

混合式連線是從函式應用程式的 [網路功能] 區段進行設定：

1. 在您剛才建立之函式應用程式的 [**設定**] 底下，選取 [**網路**]。 
1. 選取 [**設定您的混合式連接端點**]。
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="設定混合式連接端點。" border="true":::

1. 選取 [**新增混合**式連線]。
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="新增混合式連接。" border="true":::

1. 輸入混合式連線的相關資訊，如下列螢幕擷取畫面所示。 您可以選擇讓**端點主機**設定符合內部部署伺服器的主機名稱，以便在稍後執行遠端命令時更容易記住伺服器。 埠符合先前在伺服器上定義的預設 Windows 遠端系統管理服務埠。
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="新增混合式連接。" border="true":::

    | 設定      | 建議的值  |
    | ------------ | ---------------- |
    | **混合式連接名稱** | ContosoHybridOnPremisesServer |
    | **端點主機** | finance1 |
    | **端點埠** | 5986 |
    | **匯流排命名空間** | 建立新的 |
    | **位置** | 挑選可用的位置 |
    | **名稱** | contosopowershellhybrid | 

1. 選取 **[確定]** 以建立混合式連接。

## <a name="download-and-install-the-hybrid-connection"></a>下載並安裝混合式連接

1. 選取 [**下載連線管理員**]，將 *.msi*檔案儲存在您的本機電腦上。

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="下載安裝程式。" border="true":::

1. 將 *.msi*檔案從本機電腦複製到內部部署伺服器。
1. 執行混合式連線管理員安裝程式，在內部部署伺服器上安裝服務。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="安裝混合式連接。" border="true":::

1. 從入口網站開啟混合式連線，然後將閘道連接字串複製到剪貼簿。

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="複製混合式連接字串。" border="true":::

1. 在內部部署伺服器上開啟 [混合式連線管理員] UI。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="開啟 [混合式連接] UI。" border="true":::

1. 選取 [**手動輸入**]，並貼上剪貼簿中的連接字串。

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="貼上混合式連接。" border="true":::

1. 如果未顯示為已連線，請從 PowerShell 重新開機混合式連線管理員。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>建立系統管理員帳戶密碼的應用程式設定

1. 在函數應用程式的 [設定] 下 **，選取 [****設定**]。 
1. 選取 [ **+ 新增應用程式設定**]。

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="設定系統管理員帳戶的密碼。" border="true":::

1. 將設定命名為**ContosoUserPassword**，然後輸入密碼。 選取 [確定]。
1. 選取 **[儲存]** ，將密碼儲存在函數應用程式中。

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="儲存系統管理員帳戶的密碼。" border="true":::

## <a name="create-a-function-http-trigger"></a>建立函數 HTTP 觸發程式

1. 在您的函數應用程式中 **，選取 [** 函式]，然後選取 [ **+ 新增**]。

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="建立新的 HTTP 觸發程式。" border="true":::

1. 選取 [ **HTTP 觸發**程式] 範本。

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="選取 [HTTP 觸發程式] 範本。" border="true":::

1. 將新函數命名為，然後選取 [**建立函數**]。

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="命名並建立新的 HTTP 觸發程式函數。" border="true":::

## <a name="test-the-function"></a>測試函式

1. 在新函式中，選取 [程式**代碼 + 測試**]。 將範本中的 PowerShell 程式碼取代為下列程式碼：

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. 選取 [儲存]。

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="變更 PowerShell 程式碼，並儲存 HTTP 觸發程式函數。" border="true":::

 1. 選取 [**測試**]，然後選取 [**執行**] 來測試函數。 請檢查記錄檔，以確認測試是否成功。

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="測試 HTTP 觸發程式函數。" border="true":::

## <a name="managing-other-systems-on-premises"></a>管理內部部署的其他系統

您可以使用連線的內部部署伺服器，連接到本機環境中的其他伺服器和管理系統。 這可讓您使用 PowerShell 函式來管理 Azure 中的資料中心作業。 下列腳本會註冊以提供的認證執行的 PowerShell 設定會話。 這些認證必須適用于遠端伺服器上的系統管理員。 接著，您可以使用此設定來存取本機伺服器或資料中心上的其他端點。

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

將此腳本中的下列變數取代為您的環境中適用的值：
* $HybridEndpoint
* $RemoteServer

在上述兩個案例中，您可以使用 Azure Functions 和混合式連線中的 PowerShell 來連接和管理您的內部部署環境。 我們鼓勵您深入瞭解[功能中的](./functions-reference-powershell.md)[混合](../app-service/app-service-hybrid-connections.md)式連線和 PowerShell。

您也可以使用 Azure[虛擬網路](./functions-create-vnet.md)，透過 Azure Functions 連線到您的內部部署環境。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [深入瞭解如何使用 PowerShell 函式](functions-reference-powershell.md)
