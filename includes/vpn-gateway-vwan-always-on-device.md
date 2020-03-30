---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371712"
---
為了成功建立設備隧道，必須滿足以下要求：

* 設備必須是運行 Windows 10 企業版或教育版 1809 或更高版本的域。
* 隧道僅適用于 Windows 內置 VPN 解決方案，並使用具有電腦證書身份驗證的 IKEv2 建立隧道。
* 每個設備只能配置一個設備隧道。

1. 使用[點對點 VPN 用戶端](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)文章在 Windows 10 用戶端上安裝用戶端憑證。 證書需要在本地電腦存儲中。
1. 使用這些[說明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)在 LOCAL SYSTEM 帳戶的上下文中創建 VPN 設定檔並配置設備隧道。

### <a name="configuration-example-for-device-tunnel"></a>設備隧道的配置示例

配置虛擬網路閘道並在 Windows 10 用戶端上的本地電腦存儲中安裝用戶端憑證後，請使用以下示例配置用戶端設備隧道：

1. 複製以下文本並將其另存為***設備證書.ps1***。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 複製以下文本並將其保存為***VPNProfile.xml***與**設備證書.ps1**在同一資料夾中。 編輯以下文本以匹配您的環境。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. 從[系統內部](https://docs.microsoft.com/sysinternals/downloads/psexec)下載**PsExec，** 並將檔提取到**C：\PSTools。**
1. 從管理員 CMD 提示符中，通過運行啟動 PowerShell：

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. 在 PowerShell 中，切換到**設備證書.ps1**和**VPNProfile.xml**所在的資料夾，並運行以下命令：

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![機器證書測試](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. 運行**拉斯電話**。

   ![拉斯電話](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. 查找**電腦證書測試**條目，然後按一下"**連接**"。

   ![連線](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 如果連接成功，請重新開機電腦。 隧道將自動連接。
