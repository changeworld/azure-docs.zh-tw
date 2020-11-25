---
title: Azure Stack Edge 2021 年 1 月更新影響 | Microsoft Docs
description: 本文說明在安裝 2021 年 1 月更新後，對 Azure Stack Edge 裝置上的 IoT Edge 角色管理產生的影響。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578734"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Azure Stack Edge 裝置的 IoT Edge 角色管理變更

如需 Azure Stack Edge 裝置的 Azure IoT Edge 角色管理，請使用排程於2021 年 1 月發行的 API、SDK 和 Azure PowerShell 更新版本。 

本文將詳細說明您在使用這個最新版本時所需進行的變更。

2021 年 1 月更新僅適用於 Azure Stack Edge Pro - GPU、Azure Stack Edge Pro R，以及 Azure Stack Edge Mini R 裝置。 本文中的資訊僅適用於這些裝置。

> [!NOTE]
> 您不需要升級至 2021 年 1 月版本。 如果您選擇繼續使用目前的版本，則 IoT Edge 角色管理將不受影響。 不過，若要使用新功能並降低任何安全性風險，建議您安裝較新的版本。 

## <a name="iot-edge-role-management-changes"></a>IoT Edge 角色管理變更

在您的 Azure Stack Edge 裝置上安裝選用的 2021 年 1 月更新後，您必須使用最新版的 API、SDK 和 PowerShell Cmdlet 來管理 IoT Edge 角色。

只有在套用 2021 年 1 月更新時，才需要進行下列變更：

- 如果您目前使用角色管理 API &nbsp;2019-08-01 版，請升級至將在 2021 年 1 月發行的 API 版本。 
- 如果您目前使用透過 SDK &nbsp;1.0.0 版取得的角色管理，請升級至將在 2021 年 1 月發行的版本。
- 如果您使用角色管理搭配 Azure PowerShell Cmdlet (預覽)，例如 `Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole` 或 `Remove-AzStackEdgeRole`，請等待即將於 2021 年 2 月發行的新 Cmdlet。

## <a name="api-usage"></a>API 使用方式

如果您目前透過 API 執行 IoT Edge 角色管理，則應使用即將發行的新 API 版本 2020-12-01。 如果您使用目前的角色 API，在安裝即將推出的裝置軟體版本之後，您必須移至 PUT、GET、DELETE Kubernetes 角色，以及 PUT IoT 附加元件 API。

### <a name="for-the-put-method"></a>PUT 方法

#### <a name="the-current-http-request"></a>目前的 HTTP 要求 

- API 呼叫會在此 URI 上進行：'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

- 要求本文應如下所示：

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>即將推出的 HTTP 要求 

- Kubernetes 角色的 API 呼叫會在下列 URI 上進行： 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    要求本文將如下所示：

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- IoT Edge 附加元件的 API 呼叫會在下列 URI 上進行： 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    要求本文將如下所示：

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>GET 方法

#### <a name="the-current-http-response"></a>目前的 HTTP 回應

- API 呼叫是在下列 URI 上進行：

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- 回應本文應如下所示：

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>即將推出的 HTTP 回應

- API 呼叫是在下列 URI 上進行： 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- 回應本文應如下所示： 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>DELETE 方法

### <a name="the-current-api-calls"></a>目前的 API 呼叫

API 呼叫是在下列 URI 上進行： 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>即將推出的 API 呼叫

API 呼叫是在下列 URI 上進行： 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK 使用方式

如果您使用 SDK，在安裝 2021 年 1 月更新之後，您必須變更設定 IoT Edge 角色的方式，如下列範例所示。 接著，您應下載並安裝即將推出的 NuGet 套件以移至新的 SDK，如下所示。

### <a name="the-current-sdk-sample"></a>目前的 SDK 範例

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>新的 SDK 範例

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Cmdlet 使用方式

如果您目前使用 `Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole` 或 `Remove-AzStackEdgeRole` Cmdlet，則必須等候預計於 2021 年 2 月發行的新版本。

## <a name="frequently-asked-questions"></a>常見問題集

**我目前使用 Azure Stack Edge Pro - FPGA。2021 年 1 月更新是否會影響 FPGA 模型？**

否。 2021 年 1 月更新僅適用於 Azure Stack Edge Pro - FPGA、Azure Stack Edge Pro R，以及 Azure Stack Edge Mini R 裝置。 Azure Stack Edge Pro - FPGA 不會受到此更新的影響，您也無需變更 IoT Edge 角色管理。

**當我將 Azure Stack Edge Pro - GPU 更新至 2021 年 1 月的新裝置軟體之後，是否有任何現有的服務會受到影響？**

否。 在安裝 2021 年 1 月的裝置更新後，您已設定的服務不會受到影響。

**IoT Edge 管理 API、SDK 或 Cmdlet 的高階變更為何？**

IoT Edge 是 Kubernetes 角色底下的附加元件，這表示您必須先確定已設定 Kubernetes，再執行 IoT Edge 設定。


## <a name="next-steps"></a>後續步驟

- [了解如何套用更新](azure-stack-edge-gpu-install-update.md)

