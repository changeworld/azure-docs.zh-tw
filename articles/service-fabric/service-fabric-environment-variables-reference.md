---
title: Azure Service Fabric 環境變數
description: 瞭解 Azure Service Fabric 中的環境變數。 包含變數完整清單及其用法的參考。
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645679"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric 環境變數

Service Fabric 具有針對每個服務執行個體所設定的內建環境變數。 環境變數的完整清單如下：

| 環境變數                         | 描述                                                            | 範例                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | 應用程式的網狀架構 URI 名稱                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | 處理序所屬的程式碼套件名稱              | 程式碼                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | IP 位址或端點的 FQDN                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | 端點的連接埠號碼                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | 記錄資料夾                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | 暫存資料夾                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | 工作資料夾                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | 應用程式的主資料夾                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | 指定處理序是否為容器的 bool                   | false                                                                |
| Fabric_NodeId                                | 節點識別碼 (執行處理序的節點)                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP 或節點的 FQDN，如叢集資訊清單檔所指定。 | localhost 或 10.0.0.1                                                |
| Fabric_NodeName                              | 節點名稱 (執行處理序的節點)                          | _Node_0                                                              |
| Fabric_ServiceName                           | 服務的網狀架構 URI名稱 (如果服務裝載於 ExclusiveProcess 模式中)。 只有在您使用 ServicePackageActivationMode ExclusiveProcess 建立服務時，才能使用此變數值。  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | GUID                                                               |
| Fabric_ServicePackageName                    | 處理序所屬的服務套件名稱                     | Web1Pkg                                                              |

Service Fabric 執行階段所使用的內部環境變數：

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
