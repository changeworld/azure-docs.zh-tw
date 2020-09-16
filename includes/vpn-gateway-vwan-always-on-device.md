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
ms.openlocfilehash: 594ad352d5fd8431ffaf6d681c891c967cf9d32a
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606878"
---
必須符合下列需求，才能成功建立裝置通道：

* 裝置必須是執行 Windows 10 企業版或教育版1809或更新版本的已加入網域電腦。
* 通道只能針對 Windows 內建 VPN 解決方案進行設定，並使用與電腦憑證驗證的 IKEv2 建立。
* 每個裝置只能設定一個裝置通道。

1. 使用 [點對站 VPN 用戶端](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) 文章，在 Windows 10 用戶端上安裝用戶端憑證。 憑證必須在本機電腦存放區中。
1. 使用 [這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)，建立 VPN 設定檔，並在本機系統帳戶的內容中設定裝置通道。

### <a name="configuration-example-for-device-tunnel"></a>裝置通道的設定範例

設定虛擬網路閘道並將用戶端憑證安裝在 Windows 10 用戶端的本機電腦存放區之後，請使用下列範例來設定用戶端裝置通道：

1. 複製下列文字，並將它儲存為 ***devicecert.ps1***。

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
1. 複製下列文字，並將它儲存為與**devicecert.ps1**相同資料夾中的***VPNProfile.xml*** 。 編輯下列文字以符合您的環境。

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
1. 從系統管理員命令提示字元，執行下列命令來啟動 PowerShell：

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![螢幕擷取畫面顯示 [命令提示字元] 視窗，其中包含可啟動64位版 PowerShell 的命令。](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. 在 PowerShell 中，切換至 **devicecert.ps1** 和 **VPNProfile.xml** 所在的資料夾，然後執行下列命令：

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![螢幕擷取畫面顯示已使用 devicesert 腳本執行 MachineCertTest 的 PowerShell 視窗。](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. 執行 **rasphone**。

   ![螢幕擷取畫面顯示已選取 rasphone 的 [執行] 對話方塊。](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. 尋找 [ **MachineCertTest** ] 專案，然後按一下 **[連接]**。

   ![螢幕擷取畫面顯示 [網路連線] 對話方塊，其中已選取 [MachineCertTest] 和 [連線] 按鈕。](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 如果連接成功，請重新開機電腦。 通道會自動連接。
