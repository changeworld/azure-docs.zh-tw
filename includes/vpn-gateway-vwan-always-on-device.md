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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371712"
---
必須符合下列需求，才能成功建立裝置通道：

* 裝置必須是執行 Windows 10 企業版或教育版1809或更新版本的已加入網域的電腦。
* 通道只能針對 Windows 內建 VPN 解決方案進行設定，而且是使用 IKEv2 與電腦憑證驗證來建立。
* 每一裝置只能設定一個裝置通道。

1. 使用[點對站 VPN 客戶](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)端一文，在 Windows 10 用戶端上安裝用戶端憑證。 憑證必須位於本機電腦存放區中。
1. 使用[這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)，在本機系統帳戶的內容中建立 VPN 設定檔並設定裝置通道。

### <a name="configuration-example-for-device-tunnel"></a>裝置通道的設定範例

設定虛擬網路閘道並在 Windows 10 用戶端的本機電腦存放區中安裝用戶端憑證之後，請使用下列範例來設定用戶端裝置通道：

1. 複製下列文字，並將它儲存為***devicecert***。

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
1. 複製下列文字，並將它儲存為***VPNProfile***與**devicecert**相同的資料夾。 編輯下列文字以符合您的環境。

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
1. 從[Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)下載**PsExec** ，並將檔案解壓縮至**C:\PSTools**。
1. 從系統管理員命令提示字元中，執行以啟動 PowerShell：

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. 在 PowerShell 中，切換至**devicecert**和**VPNProfile**所在的資料夾，然後執行下列命令：

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. 執行**rasphone**。

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. 尋找**MachineCertTest**專案，然後按一下 **[連接]**。

   ![連線](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 如果連接成功，請將電腦重新開機。 通道會自動連接。
