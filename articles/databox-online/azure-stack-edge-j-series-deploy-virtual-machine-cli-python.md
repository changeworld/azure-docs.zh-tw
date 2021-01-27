---
title: 透過 Azure CLI 和 Python 在 Azure Stack Edge Pro GPU 裝置上部署 VM
description: 說明如何使用 Azure CLI 和 Python，在 Azure Stack Edge Pro GPU 裝置上建立和管理 (Vm) 的虛擬機器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: daf44afbb322cb30ab3a663dce4e935aefa7be13
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98808063"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>使用 Azure CLI 和 Python 在 Azure Stack Edge Pro GPU 裝置上部署 VM

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

本教學課程說明如何使用 Azure 命令列介面 (CLI) 和 Python，在 Azure Stack Edge Pro 裝置上建立和管理 VM。

## <a name="vm-deployment-workflow"></a>VM 部署工作流程

下圖說明部署工作流程。

![VM 部署工作流程](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

部署工作流程的高階摘要如下所示：

1. 連線到 Azure Resource Manager
2. 建立資源群組
3. 建立儲存體帳戶
4. 將 Blob URI 新增至 hosts 檔案
5. 安裝憑證
6. 上傳 VHD
7. 從 VHD 建立受控磁碟
8. 從映像受控磁碟建立 VM 映像
9. 使用先前建立的資源建立 VM
10. 建立 VNet
11. 使用 VNet 子網路識別碼建立 VNIC

如需工作流程圖表的詳細說明，請參閱[使用 Azure PowerShell 在 Azure Stack Edge Pro 裝置上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。 如需如何連線到 Azure Resource Manager 的相關資訊，請參閱[使用 Azure PowerShell 連線到 Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)。

## <a name="prerequisites"></a>先決條件

開始使用 Azure CLI 和 Python 在 Azure Stack Edge Pro 裝置上建立和管理 VM 之前，您必須先確定已完成下列步驟中所列的必要條件：

1. 您已在 Azure Stack Edge Pro 裝置上完成網路設定，如[步驟1：設定 Azure Stack Edge Pro 裝置](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)所述。

2. 已啟用網路介面進行計算。 此網路介面 IP 用來建立 VM 部署的虛擬交換器。 下列步驟會逐步引導您進行程序：

    1. 移至 [計算]。 選取您將用來建立虛擬交換器的網路介面。

        > [!IMPORTANT] 
        > 您只能設定一個連接埠進行計算。

    2. 在網路介面上啟用計算。 Azure Stack Edge Pro 會建立和管理對應至該網路介面的虛擬交換器。

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. 您已在 Azure Stack Edge Pro 裝置上和用戶端的信任存放區中建立和安裝所有憑證。 請遵循[步驟 2：建立和安裝用戶端憑證](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)中所述的程序。

4. 您已為 Azure Stack Edge Pro 裝置建立 Base-64 編碼的 .cer 憑證 (PEM 格式)。 該憑證已上傳為裝置上的簽署鏈，並安裝在用戶端的受根信任存放區中。 此憑證也必須為 pem 格式，Python 才能在此用戶端上運作。

    使用命令將此憑證轉換成 `pem` 格式 `certutil` 。 您必須在包含憑證的目錄中執行此命令。

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    以下顯示命令使用範例：

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    您稍後也會將它新增 `pem` 至 Python 存放區。

5. 您在裝置的本機 Web UI 中的 [網路] 頁面中指派了裝置 IP。 將此 IP 新增至：

    - 用戶端上的 hosts 檔案，或
    - DNS 伺服器組態
    
    > [!IMPORTANT]
    > 建議您修改 DNS 伺服器設定以進行端點名稱解析。

    1. 以系統管理員身分啟動 [記事本] (需有系統管理員權限才能儲存檔案)，然後開啟位於 `C:\Windows\System32\Drivers\etc` 的 **hosts** 檔案。
    
        ![Windows 檔案總管 hosts 檔案](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. 將下列項目新增至 **hosts** 檔案，並以您裝置的適當值加以取代：
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. 使用下圖進行參考。 儲存 **hosts** 檔案。

        ![記事本中的 hosts 檔案](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [下載此程序中使用的 Python 指令碼](https://aka.ms/ase-vm-python)。

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>步驟 1:在用戶端上設定 Azure CLI/Python

### <a name="verify-profile-and-install-azure-cli"></a>驗證設定檔並安裝 Azure CLI

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. 在用戶端上安裝 Azure CLI。 在此範例中，已安裝 Azure CLI 2.0.80。 若要確認 Azure CLI 的版本，請執行 `az --version` 命令。

    以下是上述命令的範例輸出：

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    如果您沒有 Azure CLI，請下載並[在 Windows 上安裝 Azure CLI](/cli/azure/install-azure-cli-windows)。 您可使用 Windows 命令提示字元或透過 Windows PowerShell 執行 Azure CLI。

2. 記下 CLI 的 Python 位置。 您需要 Python 位置，以判斷 Azure CLI 的受信任根憑證存放區的位置。

3. 若要執行本文中使用的範例指令碼，您需要下列 Python 程式庫版本：

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    若要安裝這些版本，請執行下列命令：

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    下列範例輸出顯示 Haikunator 的安裝：

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    下列範例輸出顯示 `msrestazure` 的 pip 安裝： 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>信任 Azure Stack Edge Pro CA 根憑證

1. 尋找您機器上的憑證位置。 此位置可能會根據您安裝 `az cli` 的位置不同而有所差異。 以系統管理員身分執行 Windows PowerShell。 切換至 `az cli` 安裝 Python 的路徑：`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`。

    若要取得憑證位置，請輸入下列命令：

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    此 Cmdlet 會傳回憑證位置，如下所示：  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    記下此位置，因為您稍後會用到 - `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. 將 Azure Stack Edge Pro CA 根憑證附加到現有的 Python 憑證，以信任該根憑證。 您會提供稍早儲存 PEM 憑證的路徑。

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    範例路徑會是 "C:\VM-scripts\rootteam3device.pem"
    
    然後，在 Windows PowerShell 中輸入以下一系列的命令：

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>連線至 Azure Stack Edge Pro

1. 執行 `az cloud register` 命令來註冊 Azure Stack Edge Pro 環境。

    在某些情況下，直接輸出的網際網路連線是透過 Proxy 或防火牆進行路由傳送，這會強制執行 SSL 攔截。 在這些情況下，az cloud register 命令可能會失敗並發生錯誤，例如「無法從雲端取得端點」。若要解決這個錯誤，請在 Windows PowerShell 中設定下列環境變數：

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. 為 Azure Resource Manager 端點的指令碼設定環境變數、建立資源的位置，以及來源 VHD 所在的路徑。 在所有 Azure Stack Edge Pro 裝置上，資源的位置是固定的且設定為 `dbelocal`。 您也必須指定位址前置詞和私人 IP 位址。 下列所有環境變數都是以您的值為基礎的值 `AZURE_RESOURCE_LOCATION` ，但應該將其硬式編碼為 `"dbelocal"` 。

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. 註冊您的環境。 在執行 az cloud register 時使用下列參數：

    | 值 | 說明 | 範例 |
    | --- | --- | --- |
    | 環境名稱 | 您嘗試連線的環境名稱 | 提供名稱，例如 `aze-environ` |
    | Resource Manager 端點 | 此 URL 為 `https://Management.<appliancename><dnsdomain>`。 <br> 若要取得此 URL，請移至裝置的本機 Web UI 中的 [裝置] 頁面。 |例如 `https://management.team3device.teatraining1.com`。  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    以下顯示上述命令的使用範例：
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. 使用下列命令來設定作用中環境︰

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    以下顯示上述命令的使用範例：

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. 使用 `az login` 命令來登入 Azure Stack Edge Pro 環境。 您可以以使用者身分或以[服務主體](../active-directory/develop/app-objects-and-service-principals.md)形式登入 Azure Stack Edge Pro 環境。

   遵循下列步驟，以「使用者」身分登入：

   您可以直接在 `az login` 命令內指定使用者名稱和密碼，或使用瀏覽器進行驗證。 如果您的帳戶已啟用多重要素驗證，則必須採用後者方式。

   以下顯示 `az login` 的使用範例：
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   使用 login 命令之後，系統會提示您輸入密碼。 提供 Azure Resource Manager 密碼。

   以下顯示提供密碼之後成功登入的範例輸出：  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   請記下 `id` 和值， `tenantId` 因為這些值分別對應到您的 Azure Resource Manager 訂用帳戶識別碼和 Azure Resource Manager 租使用者識別碼，並將在稍後的步驟中使用。
       
   下列環境變數必須設定為以「服務主體」的形式運作：

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   您的 Azure Resource Manager 用戶端識別碼是硬式編碼。 您的 Azure Resource Manager 租用戶識別碼和 Azure Resource Manager 訂用帳戶識別碼都會出現在您稍早執行之 `az login` 命令的輸出中。 Azure Resource Manager 用戶端密碼是您所設定的 Azure Resource Manager 密碼。

   如需詳細資訊，請參閱 [Azure Resource Manager 密碼](azure-stack-edge-j-series-set-azure-resource-manager-password.md)。

5. 將設定檔變更為版本 2019-03-01-hybrid。 若要變更設定檔版本，請執行下列命令：

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    以下顯示 `az cloud update` 的使用範例：

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>步驟 2:建立 VM

系統會提供 Python 指令碼給您建立 VM。 根據您是以使用者身分登入或設為服務主體，指令碼會據以採用輸入並建立 VM。

1. 從安裝 Python 的相同目錄執行 Python 指令碼。

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. 當指令碼執行時，上傳 VHD 需要20-30 分鐘的時間。 若要檢視上傳作業的進度，您可使用 Azure 儲存體總管或 AzCopy。

    以下是成功執行指令碼的輸出範例。 此指令碼會建立資源群組內的所有資源、使用這些資源來建立 VM，最後刪除資源群組 (包括其建立的所有資源)。

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>後續步驟

[適用於 Linux 虛擬機器的一般 Az CLI 命令](../virtual-machines/linux/cli-manage.md)