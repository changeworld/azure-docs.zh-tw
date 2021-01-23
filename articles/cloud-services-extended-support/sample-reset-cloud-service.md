---
title: 'Azure PowerShell 範例-重設 Azure 雲端服務 (延伸支援) '
description: 用於重設 Azure 雲端服務 (延伸支援) 部署的範例腳本
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a2c10e7915b0d293d3193c710885ded26a791f2c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744956"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>重設 Azure 雲端服務 (延伸支援)  
這些範例涵蓋各種不同的方式來重設現有的 Azure 雲端服務 (延伸支援) 部署。

## <a name="reimage-role-instances-of-cloud-service"></a>將雲端服務的角色實例重新安裝映射
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
此命令會映射2個角色實例 **>contosofrontend \_ in \_ 0** 和 **>contosobackend \_ in \_** a Cloud Service （名為 ContosoCS），其屬於名為 ContosOrg 的資源群組。

## <a name="reimage-all-roles-of-cloud-service"></a>重新安裝雲端服務的所有角色映射
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>重新安裝雲端服務的單一角色實例映射
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>重新開機雲端服務的單一角色實例
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>後續步驟

如需有關 Azure 雲端服務 (延伸支援) 的詳細資訊，請參閱 [Azure 雲端服務 (延伸支援) 總覽](overview.md)。