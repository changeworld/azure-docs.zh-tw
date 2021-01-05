---
title: Azure Stack Edge Pro 裝置上的 GPU Vm 的總覽和部署
description: 說明如何使用範本，在 Azure Stack Edge Pro 裝置上建立及管理 GPU 虛擬機器 (Vm) 。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 6fa8705221e1f1f9a9143f274a53db21b657988f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763778"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>適用于您 Azure Stack Edge Pro 裝置的 GPU Vm

本文概述 Azure Stack Edge Pro 裝置 (Vm) 的 GPU 虛擬機器。 本文說明如何建立 GPU VM，然後安裝 GPU 驅動程式擴充功能以安裝適當的 Nvidia 驅動程式。 使用 Azure Resource Manager 範本來建立 GPU VM，並安裝 GPU 驅動程式擴充功能。 

本文適用于 Azure Stack Edge Pro GPU 和 Azure Stack Edge Pro R 裝置。

## <a name="about-gpu-vms"></a>關於 GPU Vm

您 Azure Stack Edge Pro 裝置配備1或2個 Nvidia 的 Tesla T4 GPU。 若要在這些裝置上部署 GPU 加速的 VM 工作負載，請使用 GPU 優化的 VM 大小。 例如，您應該使用 NC T4 v3 系列來部署具有 T4 Gpu 的推斷工作負載。 

如需詳細資訊，請參閱 [NC T4 v3 系列 vm](../virtual-machines/nct4-v3-series.md)。

## <a name="supported-os-and-gpu-drivers"></a>支援的 OS 和 GPU 驅動程式 

若要利用 Azure N 系列 Vm 的 GPU 功能，必須安裝 Nvidia GPU 驅動程式。 

Nvidia GPU 驅動程式擴充功能會安裝適當的 Nvidia CUDA 或 GRID 驅動程式。 您可以使用 Azure Resource Manager 範本來安裝或管理擴充功能。

### <a name="supported-os-for-gpu-extension-for-windows"></a>適用于 Windows 的 GPU 擴充功能支援的 OS

此延伸模組支援下列作業系統 (OSs) 。 其他版本可能會運作，但尚未在內部的 Azure Stack Edge Pro 裝置上執行的 GPU Vm 進行測試。

| 散發 | 版本 |
|---|---|
| Windows Server 2019 | 核心版 |
| Windows Server 2016 | 核心版 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>適用于 Linux 的 GPU 擴充功能支援的 OS

根據特定 OS 版本的驅動程式支援，此擴充功能支援下列 OS 散發版本。 其他版本可能會運作，但尚未在內部的 Azure Stack Edge Pro 裝置上執行的 GPU Vm 進行測試。


| 散發 | 版本 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU Vm 和 Kubernetes

在裝置上部署 GPU Vm 之前，如果裝置上已設定 Kubernetes，請參閱下列考慮事項。

#### <a name="for-1-gpu-device"></a>針對1個 GPU 裝置： 

- 在 **您的裝置上建立 GPU vm 並接著 Kubernetes** 設定：在此案例中，GPU vm 的建立和 Kubernetes 設定都將成功。 在此情況下，Kubernetes 將無法存取 GPU。

- 在 **您的裝置上設定 Kubernetes，然後建立 GPU VM**：在此案例中，Kubernetes 會在您的裝置上宣告 GPU，而建立 VM 將會失敗，因為沒有可用的 gpu 資源。

#### <a name="for-2-gpu-device"></a>針對2個 GPU 裝置

- **建立 GPU vm，然後在您的裝置上建立 Kubernetes** 設定：在此案例中，您建立的 gpu vm 將會在您的裝置上宣告一個 Gpu，Kubernetes 設定也會成功，並宣告剩餘的一個 gpu。 

- **建立兩個 Gpu vm，然後在您的裝置上建立 Kubernetes** 設定：在此案例中，兩個 gpu vm 會宣告裝置上的兩個 gpu，而 Kubernetes 則設定成功且沒有 gpu。 

- 在 **您的裝置上設定 Kubernetes，然後建立 GPU VM**：在此案例中，Kubernetes 會宣告您裝置上的兩個 gpu，而建立 VM 將會失敗，因為沒有可用的 gpu 資源。

如果您有在裝置上執行的 GPU Vm，且 Kubernetes 也已設定，則每當 VM 解除配置時 (當您使用 Stop-AzureRmVM 或) New-azurermvm 移除 VM 來停止或移除 VM 時，Kubernetes 叢集將會索取裝置上所有可用的 Gpu。 在這種情況下，您將無法重新開機在裝置上部署的 GPU Vm，也不能建立 GPU Vm。


## <a name="create-gpu-vms"></a>建立 GPU Vm

在您的裝置上部署 GPU Vm 時，請遵循下列步驟：

1. 識別您的裝置是否也會執行 Kubernetes。 如果裝置將執行 Kubernetes，則您必須先建立 GPU VM，然後再設定 Kubernetes。 如果 Kubernetes 是先設定的，則會宣告所有可用的 GPU 資源，而 GPU VM 建立將會失敗。

1. 將[VM 範本和參數檔案下載](https://aka.ms/ase-vm-templates)至您的用戶端電腦。 將它解壓縮至您將作為工作目錄的目錄。

1. 若要建立 GPU Vm，請使用範本，遵循在 [Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 中的所有步驟，但有下列差異： 

    1. 設定計算網路時，請啟用連線到網際網路的埠以進行計算。 這可讓您下載 gpu Vm 的 gpu 擴充功能所需的 GPU 驅動程式。

        以下是埠2連線至網際網路，並用來啟用計算網路的範例。 如果您已識別出在先前的步驟中不需要 Kubernetes，您可以略過 Kubernetes 節點 IP 和外部服務 IP 指派。

        ![在連線到網際網路的埠上啟用計算設定](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. 使用範本建立 VM。 在指定 GPU VM 大小時，請務必使用中的 NCasT4-v3 系列， `CreateVM.parameters.json` 因為 GPU vm 支援這些功能。 如需詳細資訊，請參閱 [GPU Vm 支援的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. 當 GPU VM 建立成功之後，您可以在 Azure 入口網站的 Azure Stack Edge 資源中的虛擬機器清單中，查看此 VM。

        ![Azure 入口網站中的虛擬機器清單中的 GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. 選取 VM，並向下切入至詳細資料。 複製配置給 VM 的 IP。

    ![配置給 Azure 入口網站中 GPU VM 的 IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. 建立 VM 之後，使用延伸模組範本部署 GPU 擴充功能。 針對 linux Vm，請參閱安裝適用于 Linux 和 Windows Vm 的 [gpu 擴充](#gpu-extension-for-linux) 功能。請參閱 [安裝適用于 windows 的 gpu 擴充](#gpu-extension-for-windows)功能。

1. 若要確認 GPU 延伸模組安裝，請連線至 GPU VM：
    1. 如果使用 Windows VM，請遵循 [連接到 WINDOWS vm](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm)中的步驟。 [確認安裝](#verify-windows-driver-installation)。
    1. 如果使用 Linux VM，請遵循 [連接至 LINUX vm](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)中的步驟。 [確認安裝](#verify-linux-driver-installation)。

1. 如有需要，您可以將計算網路切換回您需要的任何內容。 


> [!NOTE]
> 將裝置軟體版本從2012更新至更新版本時，您必須手動停止 GPU Vm。


## <a name="install-gpu-extension"></a>安裝 GPU 擴充功能

視 VM 的作業系統而定，您可以安裝適用于 Windows 或 Linux 的 GPU 擴充功能。

> [!NOTE]
> 安裝 GPU 擴充功能之前，請確定您的裝置上已針對計算網路啟用的埠已連線到網際網路，且具有存取權。 GPU 驅動程式是透過網際網路存取下載的。

### <a name="gpu-extension-for-windows"></a>適用于 Windows 的 GPU 擴充功能

若要為現有的 VM 部署 Nvidia GPU 驅動程式，請編輯參數檔案， `addGPUExtWindowsVM.parameters.json` 然後再部署範本 `addGPUextensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>編輯參數檔案

檔案 `addGPUExtWindowsVM.parameters.json` 會使用下列參數：

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

以下是本文中使用的範例參數檔案：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>部署範本

部署範本 `addGPUextensiontoVM.json` 。 此範本會將擴充功能部署到現有的 VM。 執行以下命令：

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 延伸模組部署是長時間執行的作業，大約需要10分鐘的時間才能完成。

以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>追蹤部署

若要檢查指定 VM 的擴充功能部署狀態，請執行下列命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

擴充功能執行輸出會記錄至下列檔案。 請參閱此檔案 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` 以追蹤安裝的狀態。 


成功的安裝是由 `message` as `Enable Extension` 和 as 表示 `status` `success` 。

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>確認 Windows 驅動程式安裝

登入 VM，並執行隨驅動程式一起安裝的 nvidia smi-s 命令列公用程式。 `nvidia-smi.exe`位於 `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` 。 如果您看不到該檔案，可能是驅動程式安裝仍在背景中執行。 等候10分鐘，然後再檢查一次。

如果已安裝驅動程式，您會看到類似下列範例的輸出： 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

如需詳細資訊，請參閱 [適用于 Windows 的 NVIDIA GPU 驅動程式擴充](../virtual-machines/extensions/hpccompute-gpu-windows.md)功能。

### <a name="gpu-extension-for-linux"></a>適用于 Linux 的 GPU 擴充功能

若要為現有的 VM 部署 Nvidia GPU 驅動程式，請編輯參數檔案， `addGPUExtLinuxVM.parameters.json` 然後再部署範本 `addGPUextensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>編輯參數檔案

如果使用 Ubuntu，檔案 `addGPUExtLinuxVM.parameters.json` 會採用下列參數：

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
如果使用 Red Hat Enterprise Linux (RHEL) ，file 會採用下列參數：


```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


以下是本文中使用的範例 Ubuntu 參數檔案：

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>部署範本

部署範本 `addGPUextensiontoVM.json` 。 此範本會將擴充功能部署到現有的 VM。 執行以下命令：

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 延伸模組部署是長時間執行的作業，大約需要10分鐘的時間才能完成。

以下是範例輸出：

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>追蹤部署狀態    
    
範本部署是長時間執行的作業。 若要檢查指定 VM 的擴充功能部署狀態，請開啟另一個 PowerShell 會話， (以系統管理員身分執行) 。 執行以下命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
以下是範例輸出： 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 當部署完成時，會 `ProvisioningState` 變更為 `Succeeded` 。

擴充功能執行輸出會記錄至下列檔案： `/var/log/azure/nvidia-vmext-status` 。

#### <a name="verify-linux-driver-installation"></a>確認 Linux 驅動程式安裝

請遵循下列步驟來確認驅動程式安裝：

1. 連接至 GPU VM。 遵循 [連接至 LINUX VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)中的指示。 

    以下是範例輸出：

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. 執行隨驅動程式一起安裝的 nvidia smi-s 命令列公用程式。 如果已成功安裝驅動程式，您將能夠執行公用程式，並看到下列輸出：

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

如需詳細資訊，請參閱 [適用于 Linux 的 NVIDIA GPU 驅動程式擴充](../virtual-machines/extensions/hpccompute-gpu-linux.md)功能。

## <a name="remove-gpu-extension"></a>移除 GPU 擴充功能

若要移除 GPU 擴充功能，請使用下列命令：

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

以下是範例輸出：

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>後續步驟

[Azure Resource Manager Cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)