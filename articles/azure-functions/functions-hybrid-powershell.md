---
title: 使用 PowerShell 函數管理遠端內部部署資源
description: 瞭解如何在 Azure 轉送中設定混合式連線，以將 PowerShell 函式應用程式連線到內部部署資源，然後用來從遠端系統管理內部部署資源。
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74226943"
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

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取左側功能表中的 [ **+ 建立資源**]，然後選取 [**函數應用程式**]。

1. 針對 [**主控方案**]，選取 [ **App Service 方案**]，然後選取 [ **App Service 方案/位置**]。

1. 選取 [**新建**]，輸入**App Service 方案**名稱，選擇您的函式在您的[區域](https://azure.microsoft.com/regions/)附近或接近您函數存取的其他服務**位置**，然後選取 [**定價層**]。

1. 選擇 S1 標準方案，然後選取 [套用 **]。**

1. 選取 **[確定]** 以建立方案，然後依照下列螢幕擷取畫面的內容，設定下表中所指定的其餘**函數應用程式**設定：

    ![PowerShell Core 函數應用程式](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元是 `a-z`、`0-9` 和 `-`。  | 
    | **訂用帳戶** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **資源群組** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 您也可以使用建議的值。 |
    | **OS** | 慣用的作業系統 | 選取 [Windows]。 |
    | **執行階段堆疊** | 慣用語言 | 選擇 [PowerShell Core]。 |
    | **儲存體** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 儲存體帳戶名稱的長度必須介於3到24個字元之間，而且只能包含數位和小寫字母。 您也可以使用現有帳戶。
    | **Application Insights** | 預設 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 藉由展開此設定，您可以變更**新的資源名稱**，或在您想要儲存資料的[Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)區域中選擇不同的**位置**。 |

1. 驗證設定之後，請選取 [**建立**]。

1. 選取入口網站右上角的 [**通知**] 圖示，然後等候「部署成功」訊息。

1. 選取 [前往資源]  ，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]  。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>建立函數應用程式的混合式連線

混合式連線是從函式應用程式的 [網路功能] 區段進行設定：

1. 選取函數應用程式中的 [**平臺功能**] 索引標籤，然後選取 [**網路**]。 
   ![平臺網路的應用程式總覽](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. 選取 [**設定您的混合式連接端點**]。
   ![網路功能](./media/functions-hybrid-powershell/select-network-feature.png)  
1. 選取 [**新增混合**式連線]。
   ![混合式連接](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. 輸入混合式連線的相關資訊，如下列螢幕擷取畫面所示。 您可以選擇讓**端點主機**設定符合內部部署伺服器的主機名稱，以便在稍後執行遠端命令時更容易記住伺服器。 埠符合先前在伺服器上定義的預設 Windows 遠端系統管理服務埠。
  ![新增混合式連線](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **混合式連接名稱**： ContosoHybridOnPremisesServer
    
    **端點主機**： finance1
    
    **端點埠**：5986
    
    執行**匯流排命名空間**：新建
    
    **位置**：挑選可用的位置
    
    **名稱**： contosopowershellhybrid

5. 選取 **[確定]** 以建立混合式連接。

## <a name="download-and-install-the-hybrid-connection"></a>下載並安裝混合式連接

1. 選取 [**下載連線管理員**]，將 .msi 檔案儲存在您的本機電腦上。
![下載安裝程式](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. 將 .msi 檔案從本機電腦複製到內部部署伺服器。
1. 執行混合式連線管理員安裝程式，在內部部署伺服器上安裝服務。
![安裝混合式連接](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. 從入口網站開啟混合式連線，然後將閘道連接字串複製到剪貼簿。
![複製混合式連接字串](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. 在內部部署伺服器上開啟 [混合式連線管理員] UI。
![開啟混合式連接 UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. 選取 [**手動輸入**] 按鈕，然後貼上剪貼簿中的連接字串。
![貼上連接](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. 如果未顯示為已連線，請從 PowerShell 重新開機混合式連線管理員。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>建立系統管理員帳戶密碼的應用程式設定

1. 選取函數應用程式中的 [**平臺功能**] 索引標籤。
1. 在 **[一般設定**] 底下 **，選取**[設定]。
![選取平臺設定](./media/functions-hybrid-powershell/select-configuration.png)  
1. 展開 [**新增應用程式設定**]，以建立新的密碼設定。
1. 將設定命名為_ContosoUserPassword_，然後輸入密碼。
1. 選取 **[確定]** ，然後按一下 [儲存]，將密碼儲存在函數應用程式中。
![新增密碼的應用程式設定](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>建立函數 HTTP 觸發程式以進行測試

1. 從函式應用程式建立新的 HTTP 觸發程式函式。
![建立新的 HTTP 觸發程式](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. 將範本中的 PowerShell 程式碼取代為下列程式碼：

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

3. 選取 [**儲存**並**執行**] 來測試函數。
![測試函數應用程式](./media/functions-hybrid-powershell/test-function-hybrid.png)  

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
