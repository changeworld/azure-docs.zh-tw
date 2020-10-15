---
title: Azure Service Fabric CLI (sfctl) 指令碼移除範例
description: 使用 Azure Service Fabric CLI 將應用程式從 Azure Service Fabric 叢集中移除
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
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: fd5d20ed3f2cc41f4d7d51f06d4bc90a6afd22eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75526527"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-the-service-fabric-cli"></a>使用 Service Fabric CLI 將應用程式從 Service Fabric 叢集中移除

這個範例指令碼會刪除一個執行中的 Service Fabric 應用程式執行個體，並將叢集中的一個應用程式類型和版本取消註冊。  刪除應用程式執行個體也會刪除應用程式相關聯的所有執行中服務執行個體。 接下來，系統會從映像存放區刪除應用程式檔案。 

如有需要，請安裝[ Service Fabric SDK](../service-fabric-cli.md)。

## <a name="sample-script"></a>範例指令碼

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Service Fabric CLI 文件](../service-fabric-cli.md)。

您可以在 [Service Fabric CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Service Fabric CLI 範例。
