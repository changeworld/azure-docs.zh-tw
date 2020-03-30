---
title: 由不支援 TLS 1.2 的應用程式引起的故障排除問題 |微軟文檔
description: 由不支援 TLS 1.2 的應用程式引起的故障排除問題
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/16/2020
ms.author: tagore
ms.openlocfilehash: 6153b9d5e8ef11412b0dd53a15c565becfa1c8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053762"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>對不支援 TLS 1.2 的應用程式進行故障排除
本文介紹如何啟用較舊的 TLS 協定（TLS 1.0 和 1.1），以及應用舊密碼套件來支援 Windows Server 2019 雲服務 Web 和輔助角色上的其他協定。 

我們理解，雖然我們正在採取措施棄用 TLS 1.0 和 TLS 1.1，但我們的客戶可能需要支援較舊的協定和密碼套件，直到他們能夠計畫棄用。  雖然我們不建議重新啟用這些舊值，但我們提供指導來説明客戶。 我們鼓勵客戶在實施本文中概述的更改之前評估回歸風險。 

> [!NOTE]
> 來賓 OS 系列 6 版本通過顯式禁用 TLS 1.0 和 1.1 並定義一組特定的密碼套件來強制執行 TLS 1.2。有關客體作業系統系列的詳細資訊，請參閱[客體作業系統發佈新聞](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-6-releases)


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>放棄對 TLS 1.0、TLS 1.1 和舊密碼套件的支援 
為了支援我們使用同類最佳加密的承諾，Microsoft 于 2017 年 6 月宣佈計畫從 TLS 1.0 和 1.1 開始遷移。   自最初宣佈以來，Microsoft 宣佈我們打算在 2020 年上半年在支援的 Microsoft Edge 和 IEE 11 支援版本中禁用傳輸層安全性 （TLS） 1.0 和 1.1。  蘋果、谷歌和Mozilla的類似公告表明了該行業的發展方向。   

有關詳細資訊，請參閱在[Microsoft Azure 中準備 TLS 1.2](https://azure.microsoft.com/updates/azuretls12/)

## <a name="tls-configuration"></a>TLS 配置  
Windows Server 2019 雲伺服器映射配置了 TLS 1.0 和 TLS 1.1，在註冊表級別禁用。 這意味著部署到此版本的 Windows 並使用 Windows 堆疊進行 TLS 協商的應用程式將不允許 TLS 1.0 和 TLS 1.1 通信。   

伺服器還附帶一組有限的密碼套件： 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>第 1 步：創建 PowerShell 腳本以啟用 TLS 1.0 和 TLS 1.1 

使用以下代碼作為示例創建啟用舊協定和密碼套件的腳本。 對於本文檔的目的，此腳本將命名為 **：TLSsettings.ps1**。 將此腳本存儲在本地桌面上，以便稍後步驟輕鬆訪問。 


```Powershell
# You can use the -SetCipherOrder (or -sco) option to also set the TLS cipher 
# suite order. Change the cipherorder variable below to the order you want to set on the 
# server. Setting this requires a reboot to take effect.

Param(
 [parameter(Mandatory=$false)]
 [alias("sco")]
 [switch]$SetCipherOrder)

 Function DisableRC4 {
   param ( $restart)
  $subkeys = Get-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL"
  $ciphers = $subkeys.OpenSubKey("Ciphers", $true)

  if($ciphers.SubKeyCount -eq 0) {
    $k1 = $ciphers.CreateSubKey("RC4 128/128")
    $k1.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $restart = $true
    $k2 = $ciphers.CreateSubKey("RC4 64/128")
    $k2.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k3 = $ciphers.CreateSubKey("RC4 56/128")
    $k3.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k4 = $ciphers.CreateSubKey("RC4 40/128")
    $k4.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
  }

  $restart

}

Function Set-CryptoSetting {
  param (
    $keyindex,
    $value,
    $valuedata,
    $valuetype,
    $restart
  )

  # Check for existence of registry key, and create if it does not exist
  If (!(Test-Path -Path $regkeys[$keyindex])) {
    New-Item $regkeys[$keyindex] | Out-Null
  }

  # Get data of registry value, or null if it does not exist
  $val = (Get-ItemProperty -Path $regkeys[$keyindex] -Name $value -ErrorAction SilentlyContinue).$value

  If ($null -eq $val) {
    # Value does not exist - create and set to desired value
    New-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null
    $restart = $True
    Write-Host "Configuring $regkeys[$keyindex]...."

  } Else {

    # Value does exist - if not equal to desired value, change it
    If ($val -ne $valuedata) {
      Set-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata
      $restart = $True
      Write-Host "Configuring $regkeys[$keyindex]..."
    }
  }

  $restart

}

$regkeys = @(
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server", #2
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", #4
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",        #6
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server", #8
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Client", #10
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0",        #12
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Server", #14
"HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"
)

Function Set-Windows10PlusCurveOrder {
    param ( $reboot)
    $desiredOrder = "NistP384;NistP256".Split(";")
    If ([Environment]::OSVersion.Version.Major -ge 10) {
        If (!(Test-Path -Path $regkeys[15])) {
            New-Item $regkeys[15] | Out-Null
            $reboot = $True
        }

        $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("EccCurves", $null)

        if( $null -eq $val) {
            New-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder -PropertyType MultiString | Out-Null
            $reboot = $True

        } else {

            if ([System.String]::Join(';', $val) -ne [System.String]::Join(';', $desiredOrder)) {
                Write-Host "The original curve order ", `n, $val, `n, "needs to be updated to ", $desiredOrder
                Set-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder
                $reboot = $True
            }
        }
    }

    $reboot

}

If ([Environment]::OSVersion.Version.Major -lt 10) {
  # This is for Windows before 10 
  Write-Host "Configuring Windows before 10..."
  $cipherorder =  "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"

} Else {

 # this is for windows 10 or above
 Write-Host "Configuring Windows 10+..."
 $cipherorder = "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"
}

# If any settings are changed, this will change to $True and the server will reboot
$reboot = $False

# Check for existence of registry keys (SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2), and create if they do not exist
For ($i = 0; $i -le 14; $i = $i + 1) {
  If (!(Test-Path -Path $regkeys[$i])) {
    New-Item $regkeys[$i] | Out-Null
  }
}

# Ensure SSL 2.0 disabled for client/server
$reboot = Set-CryptoSetting 10 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 10 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 11 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 11 Enabled 0 DWord $reboot

# Ensure SSL 3.0 disabled for client/server
$reboot = Set-CryptoSetting 13 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 13 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 14 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 14 Enabled 0 DWord $reboot

# Ensure TLS 1.0 enabled for client/server
$reboot = Set-CryptoSetting 1 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 1 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 2 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 2 Enabled 1 DWord $reboot

# Ensure TLS 1.1 enabled for client/server
$reboot = Set-CryptoSetting 4 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 4 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 5 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 5 Enabled 1 DWord $reboot

# Ensure TLS 1.2 enabled for client/server
$reboot = Set-CryptoSetting 7 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 7 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 8 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 8 Enabled 1 DWord $reboot

$reboot = DisableRC4($reboot)

If ($SetCipherOrder) {
      If (!(Test-Path -Path $regkeys[15])) {
        New-Item $regkeys[15] | Out-Null
        $reboot = $True
      }

      $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("Functions", $null)

      if ($val -ne $cipherorder)
      {
        Write-Host "The original cipher suite order needs to be updated", `n, $val
        Set-ItemProperty -Path $regkeys[15] -Name Functions -Value $cipherorder
        $reboot = $True
      }
  }

$reboot = Set-Windows10PlusCurveOrder $reboot

If ($reboot) {
  # Randomize the reboot timing since it could be run in a large cluster.
  $tick = [System.Int32]([System.DateTime]::Now.Ticks % [System.Int32]::MaxValue)
  $rand = [System.Random]::new($tick)
  $sec = $rand.Next(30, 600)
  Write-Host "Rebooting after", $sec, " second(s)..."
  Write-Host  "shutdown.exe /r /t $sec /c ""Crypto settings changed"" /f /d p:2:4"
  shutdown.exe /r /t $sec /c "Crypto settings changed" /f /d p:2:4

} Else {

  Write-Host "Nothing get updated."
}
```

## <a name="step-2-create-a-command-file"></a>第 2 步：創建命令檔 

使用以下創建名為**RunTLSSettings.cmd**的 CMD 檔。 將此腳本存儲在本地桌面上，以便稍後步驟輕鬆訪問。 

```cmd
SET LOG_FILE="%TEMP%\StartupLog.txt"
SET EXECUTE_PS1=0

IF "%ComputeEmulatorRunning%" == "" (
       SET EXECUTE_PS1=1
)

IF "%ComputeEmulatorRunning%" == "false" (
       SET EXECUTE_PS1=1
) 

IF %EXECUTE_PS1% EQU 1 (
       echo "Invoking TLSsettings.ps1 on Azure service at %TIME% on %DATE%" >> %LOG_FILE% 2>&1       
       PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1 -sco  >> %LOG_FILE% 2>&1
) ELSE (
       echo "Skipping TLSsettings.ps1 invocation on emulated environment" >> %LOG_FILE% 2>&1       
)    

EXIT /B %ERRORLEVEL%

```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>步驟 3：將啟動任務添加到角色的服務定義 （csdef） 

將以下程式碼片段添加到現有服務定義檔中。 

```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

下面是一個同時顯示輔助角色和 Web 角色的示例。 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-add-the-scripts-to-your-cloud-service"></a>第 4 步：將腳本添加到雲服務 

1) 在視覺化工作室中，按右鍵 WebRole 或輔助角色
2) 選擇 **"添加"**
3) 選擇**現有專案**
4) 在檔資源管理器中，導航到存儲**TLSsettings.ps1**和**RunTLSSettings.cmd**檔的桌面 
5) 選擇要將其添加到雲服務專案的兩個檔

## <a name="step-5-enable-copy-to-output-directory"></a>步驟 5：啟用複製到輸出目錄

為確保腳本在從 Visual Studio 推送的每個更新時上載，需要將"*複製到輸出目錄*"設置為 *"始終複製*"

1) 在 WebRole 或輔助角色下，按右鍵 RunTLSSettings.cmd
2) 選擇**屬性**
3) 在屬性選項卡中，將 *"複製到輸出目錄"* 更改為 *"始終複製"*
4) 重複**TLS 設置的步驟。**

## <a name="step-6-publish--validate"></a>第 6 步：發佈&驗證

完成上述步驟後，將更新發佈到現有雲服務。 

您可以使用[SSLLabs](https://www.ssllabs.com/)驗證終結點的 TLS 狀態 

 
