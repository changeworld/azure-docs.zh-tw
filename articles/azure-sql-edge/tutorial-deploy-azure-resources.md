---
title: 設定資源以在 Azure SQL Edge 中部署 ML 模型
description: 在此 Azure SQL Edge 教學課程三部曲的第一部分中，您會在 Azure SQL Edge 中安裝必要軟體並設定必要的 Azure 資源，以部署機器學習模型。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 8c17351c049ef419ab95f46a54aa9f8cc523f7cf
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887447"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>安裝軟體並設定教學課程的資源

在此教學課程三部曲中，您會建立機器學習模型以預測鐵礦中的雜質含量 (以二氧化硅的百分比表示)，然後在 Azure SQL Edge 中部署模型。 在第一個部分中，您會安裝必要軟體和部署 Azure 資源。

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。
2. 安裝 [Python 3.6.8](https://www.python.org/downloads/release/python-368/)。
      * 使用 Windows x86-x64 可執行檔安裝程式
      * 將 `python.exe` 新增至 PATH 環境變數路徑下載中。 您可以在「適用於 Visual Studio 2019 的工具」下找到下載。
3. 安裝[適用於 SQL Server 的 Microsoft ODBC Driver 17](https://www.microsoft.com/download/details.aspx?id=56567)。
4. 安裝 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/)
5. 開啟 Azure Data Studio，並設定適用於 Notebooks 的 Python。 如需詳細資訊，請參閱[設定適用於 Notebooks 的 Python](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks)。此步驟可能需要幾分鐘的時間。
6. 安裝最新版的 [Azure CLI](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020)。 下列指令碼需要最新版本的 AZ PowerShell  (3.5.0，2020年 2 月)。
7. 下載將在本教學課程中使用的 [DACPAC](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/DACPAC) 及 [AMD/ARM Docker 映像檔](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/tar-files)。

## <a name="deploy-azure-resources-using-powershell-script"></a>使用 PowerShell 指令碼部署 Azure 資源

部署此 Azure SQL Edge 教學課程所需的 Azure 資源。 您可以使用 PowerShell 指令碼或透過 Azure 入口網站來部署這些資源。 本教學課程使用 PowerShell 指令碼。

1. 匯入在本教學課程中執行 PowerShell 指令碼所需的模組。

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. 宣告 PowerShell 指令碼所需的變數。

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. 宣告其餘變數。

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. 若要開始建立資產，請登入 Azure。

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. 設定 Azure 訂用帳戶識別碼。

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. 建立資源群組 (若尚不存在)。

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. 在資源群組中建立儲存體帳戶和儲存體帳戶容器。

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. 將資料庫 `dacpac` 檔案上傳至儲存體帳戶，並產生 Blob 的 SAS URL。 **記下資料庫 `dacpac` Blob 的 SAS URL。**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. 在此資源群組中建立 Azure 容器登錄。

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. 將 ARM/AMD Docker 映像發送至容器登錄。

    ```powershell
    $containerRegistryCredentials = Get-AzContainerRegistryCredential -ResourceGroupName $ResourceGroup -Name $containerRegistryName
    
    $amddockerimageFile = Read-Host "Please Enter the location to the amd docker tar file:"
    $armdockerimageFile = Read-Host "Please Enter the location to the arm docker tar file:"
    $amddockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":amd64"
    $armdockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":arm64"
    
    docker login $containerRegistry.LoginServer --username $containerRegistryCredentials.Username --password $containerRegistryCredentials.Password
    
    docker import $amddockerimageFile $amddockertag
    docker push $amddockertag
    
    docker import $armdockerimageFile $armdockertag
    docker push $armdockertag
    ```

11. 在資源群組中建立網路安全性群組。

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

12. 建立已透過 SQL Edge 啟用的 Azure 虛擬機器。 此 VM 將作為 Edge 裝置。

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

13. 在資源群組中建立 IoT 中樞。

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

14. 將 Edge 裝置新增至 IoT 中樞。 此步驟只會建立裝置的數位身分識別。

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

15. 取得裝置主要連接字串。 稍後 VM 將會需要此資訊。 下列命令會使用 Azure CLI 進行部署。

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

16. 更新 Edge 裝置上 IoT Edge 組態檔中的連接字串。 下列命令會使用 Azure CLI 進行部署。

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

17. 在資源群組中建立 Azure Machine Learning 工作區。

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```

## <a name="next-steps"></a>後續步驟

* [設定 IoT Edge 模組和連線](tutorial-set-up-iot-edge-modules.md)
