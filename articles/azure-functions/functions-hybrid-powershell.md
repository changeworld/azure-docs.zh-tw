---
title: 使用 PowerShell 函數管理遠端本地資源
description: 瞭解如何在 Azure 中繼中配置混合連接，將 PowerShell 函數應用連接到本地資源，然後可用於遠端系統管理本地資源。
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226943"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>在 Azure 函數和應用服務混合連接中使用 PowerShell 管理混合環境

Azure 應用服務混合連接功能允許訪問其他網路中的資源。 您可以在[混合連接](../app-service/app-service-hybrid-connections.md)文檔中瞭解有關此功能的更多詳細資訊。 本文介紹如何使用此功能運行面向本機伺服器的 PowerShell 函數。 然後，此伺服器可用於從 Azure PowerShell 函數管理本地環境中的所有資源。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>配置用於 PowerShell 遠端處理的本機伺服器

以下腳本支援 PowerShell 遠端處理，並創建新的防火牆規則和 WinRM HTTPs 攔截器。 出於測試目的，使用自簽章憑證。 在生產環境中，我們建議您使用簽名的證書。

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

## <a name="create-a-powershell-function-app-in-the-portal"></a>在門戶中創建 PowerShell 函數應用

應用服務混合連接功能僅在基本、標準和隔離定價計畫中可用。 使用 PowerShell 創建函數應用時，請創建或選擇這些計畫之一。

1. 在[Azure 門戶](https://portal.azure.com)中，選擇 " 在左側的功能表中**創建資源**"，然後選擇 **"功能"應用**。

1. 對於**託管計畫**，選擇**應用服務方案**，然後選擇**應用服務方案/位置**。

1. 選擇 **"創建新**"，鍵入**應用服務方案**名稱，選擇靠近您[的區域](https://azure.microsoft.com/regions/)或函數訪問的其他服務附近的**位置**，然後選擇**定價層**。

1. 選擇 S1 標準計畫，然後選擇 **"應用**"。

1. 選擇 **"確定"** 以創建計畫，然後在以下螢幕截圖後立即配置表中指定的其餘**功能應用**設置：

    ![PowerShell 核心功能應用](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以識別新函式應用程式的名稱。 有效字元是 `a-z`、`0-9` 和 `-`。  | 
    | **訂閱** | 您的訂用帳戶 | 將在其下建立這個新函式應用程式的訂用帳戶。 |
    | **資源組** |  myResourceGroup | 要在其中建立函式應用程式的新資源群組名稱。 您還可以使用建議的值。 |
    | **作業系統** | 首選作業系統 | 選取 [Windows]。 |
    | **執行階段堆疊** | 慣用語言 | 選擇強力外殼核心。 |
    | **儲存空間** |  全域唯一的名稱 |  建立您函式應用程式使用的儲存體帳戶。 存儲帳戶名稱的長度必須為 3 到 24 個字元，並且只能包含數位和小寫字母。 您也可以使用現有帳戶。
    | **Application Insights** | 預設 | 在最近的支援區域中，建立相同*應用程式名稱*的 Application Insights 資源。 通過展開此設置，可以更改 **"新建資源"名稱**，或在要存儲資料的 Azure[地理](https://azure.microsoft.com/global-infrastructure/geographies/)區域中選擇其他**位置**。 |

1. 驗證設置後，選擇 **"創建**"。

1. 選擇門戶右上角**的通知**圖示，然後等待"部署成功"消息。

1. 選取 [前往資源]****，以檢視您新的函式應用程式。 您也可以選取 [釘選到儀表板]****。 釘選可讓您更輕鬆地從儀表板返回此函式應用程式資源。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>為函數應用創建混合連接

混合連接從功能應用的網路部分配置：

1. 在功能應用中選擇 **"平臺功能**"選項卡，然後選擇 **"網路**"。 
   ![平臺網路的應用概述](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. 選擇 **"配置混合連接終結點**"。
   ![網路功能](./media/functions-hybrid-powershell/select-network-feature.png)  
1. 選擇 **"添加混合連接**"。
   ![混合連接](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. 輸入有關混合連接的資訊，如下圖所示。 您可以選擇使**終結點主機**設置與本機伺服器的主機名稱匹配，以便以後在運行遠端命令時更容易記住伺服器。 該埠與之前在伺服器上定義的預設 Windows 遠端系統管理服務埠匹配。
  ![新增混合式連線](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **混合連接名稱**： Contoso 混合本機伺服器
    
    **端點主機**：財務1
    
    **端點埠**： 5986
    
    **服務匯流排命名空間**： 創建新
    
    **位置**： 選擇可用位置
    
    **名稱**： 孔力殼混合體

5. 選擇 **"確定"** 以創建混合連接。

## <a name="download-and-install-the-hybrid-connection"></a>下載並安裝混合連接

1. 選擇 **"下載連線管理員**"以將 .msi 檔案本地保存到電腦上。
![下載安裝程式](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. 將 .msi 檔案從本地電腦複製到本機伺服器。
1. 運行混合連線管理員安裝程式以在本機伺服器上安裝該服務。
![安裝混合連接](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. 從門戶打開混合連接，然後將閘道連接字串複製到剪貼簿。
![複製混合連接字串](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. 在本機伺服器上打開混合連線管理員 UI。
![打開混合連接 UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. 選擇"**手動輸入"** 按鈕，然後從剪貼簿粘貼連接字串。
![粘貼連接](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. 如果混合連線管理員未顯示為已連接，請從 PowerShell 重新開機它。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>為管理員帳戶的密碼創建應用設置

1. 在功能應用中選擇 **"平臺功能**"選項卡。
1. 在 **"常規設置"** 下，選擇 **"配置**"。
![選擇平台佈建](./media/functions-hybrid-powershell/select-configuration.png)  
1. 展開 **"新建應用程式"設置**以為密碼創建新設置。
1. 為設置_ContosoUser 密碼_命名，然後輸入密碼。
1. 選擇 **"確定"，** 然後保存以將密碼存儲在函數應用程式中。
![添加密碼的應用設置](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>創建函數 HTTP 觸發器以進行測試

1. 從函數應用創建新的 HTTP 觸發器函數。
![創建新的 HTTP 觸發器](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. 將範本中的 PowerShell 代碼替換為以下代碼：

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

3. 選擇 **"保存**並**運行**"以測試該功能。
![測試函數應用](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>在本地管理其他系統

您可以使用連接的本機伺服器連接到本地環境中的其他伺服器和管理系統。 這允許您使用 PowerShell 函數從 Azure 管理資料中心操作。 以下腳本註冊在提供的憑據下運行的 PowerShell 配置會話。 這些憑據必須適用于遠端伺服器上的管理員。 然後，可以使用此配置訪問本機伺服器或資料中心上的其他終結點。

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

將此腳本中的以下變數替換為環境中的適用值：
* $HybridEndpoint
* $RemoteServer

在前面的兩個方案中，您可以通過在 Azure 函數和混合連接中使用 PowerShell 來連接和管理本地環境。 我們鼓勵您在功能中瞭解有關[混合連接](../app-service/app-service-hybrid-connections.md)和[PowerShell](./functions-reference-powershell.md)的更多。

您還可以使用 Azure[虛擬網路](./functions-create-vnet.md)通過 Azure 函數連接到本地環境。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [瞭解有關使用 PowerShell 功能的更多詳細資訊](functions-reference-powershell.md)
