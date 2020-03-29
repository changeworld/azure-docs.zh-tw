---
title: 查找 Azure 服務結構網格示例
description: 下面是可用服務結構網格應用程式範例的索引。 這些範例中的原始程式碼可說明如何使用 Service Fabric 資源模型達成特定案例。
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461899"
---
# <a name="find-service-fabric-mesh-samples"></a>尋找 Service Fabric Mesh 範例

下表列出可用的 Service Fabric Mesh 範例應用程式。 這些範例中的原始程式碼可說明如何使用 Service Fabric 資源模型達成特定案例。

若想進一步了解直接部署至 Azure 的部署範本，請參閱[範例範本的 GitHub 頁面](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)。

|範例範本|案例描述|原始程式碼|開發人員工具|
|------------|--------------------|----------|----------------------|
| [Hello World 應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | 裝載於容器中的靜態網頁。 在 Linux 上它會使用 nginx，在 Windows 上則使用 IIS | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 沒有需求 |
| [Azure 檔案磁碟區的計數器應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | 在容器中掛接以 Azure 檔案為基礎的磁碟區來儲存狀態。 <br><br> **注意︰** 要使用此範本，必須已佈建 Azure 檔案的檔案共用：[指示](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 工具 |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | 建立具有前端和後端服務 (使用 DNS 解析) 的應用程式。 請參閱[這裡](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore)的教學課程 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh 工具 |
| [視覺物件應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | 調整和升級應用程式內的微服務。 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh 工具 |
| [投票應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | 建立具有前端和後端服務 (使用 DNS 解析) 的應用程式 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | 適用于 Windows 版本的視覺化工作室網格工具，VS 代碼/點網 CLI 可用於 Linux 版本 |
| [Service Fabric 可靠磁碟區的計數器應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| 在容器中掛接以 Service Fabric 可靠磁碟為基礎的磁碟區來儲存狀態。| [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 工具 |
