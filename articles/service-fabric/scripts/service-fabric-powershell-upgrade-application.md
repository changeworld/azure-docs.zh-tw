---
title: 在 PowerShell 中升級 Service Fabric 應用程式
description: Azure PowerShell 指令碼範例 - 使用 PowerShell 升級和監視 Azure Service Fabric 應用程式。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1b04a0c4d2865cf72d9fbccb51f0f083872ea799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87037877"
---
# <a name="upgrade-a-service-fabric-application"></a>升級 Service Fabric 應用程式

這個範例指令碼會將執行中的 Service Fabric 應用程式執行個體升級到 1.3.0 版。 指令碼會將新的應用程式套件複製到叢集映像存放區、註冊應用程式類型，並移除不必要的應用程式套件。  指令碼會啟動受監視的升級程序，並持續檢查升級狀態，直到升級完成或復原為止。 視需要自訂參數。 

如有需要，可隨同 [Service Fabric SDK](../service-fabric-get-started.md) 一起安裝 Service Fabric PowerShell 模組。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | 取得 Service Fabric 叢集或特定應用程式中的所有應用程式。  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | 取得 Service Fabric 應用程式升級的狀態。 |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | 取得已在 Service Fabric 叢集上註冊的 Service Fabric 應用程式類型。 |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 取消註冊 Service Fabric 應用程式類型。  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 將 Service Fabric 應用程式套件複製到映像存放區。  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | 註冊 Service Fabric 應用程式類型。 |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | 將 Service Fabric 應用程式升級至指定的應用程式類型版本。 |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 從映像存放區移除 Service Fabric 應用程式封裝。|


## <a name="next-steps"></a>後續步驟

如需有關 Service Fabric PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/service-fabric/overview?view=azureservicefabricps)。

您可以在 [PowerShell 範例](../service-fabric-powershell-samples.md)中找到適用於 Azure Service Fabric 的其他 Azure PowerShell 範例。
