---
title: Azure Service Fabric CLI-sfctl
description: 深入瞭解 sfctl，這是 Azure Service Fabric 命令列介面。 包含命令和子群組的清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906214"
---
# <a name="sfctl"></a>sfctl
用於管理 Service Fabric 叢集和實體的命令。 這個版本與 Service Fabric 7.0 執行時間相容。

命令會依循「名詞-動詞」模式。 如需詳細資訊，請參閱＜子群組＞。

## <a name="subgroups"></a>子群組
|子群組|說明|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | 建立、刪除與管理應用程式和應用程式類型。 |
| [chaos](service-fabric-sfctl-chaos.md) | 啟動、停止及報告 chaos 測試服務。 |
| [cluster](service-fabric-sfctl-cluster.md) | 選取、管理和操作 Service Fabric 叢集。 |
| [compose](service-fabric-sfctl-compose.md) | 建立、刪除和管理 Docker Compose 應用程式。 |
| [container](service-fabric-sfctl-container.md) | 在叢集節點上執行容器相關命令。 |
| [events](service-fabric-sfctl-events.md) | 從事件存放區取出事件（如果已安裝 EventStore 服務）。 |
| [is](service-fabric-sfctl-is.md) | 查詢命令，並將其傳送至基礎結構服務。 |
| [mesh](service-fabric-sfctl-mesh.md) | 刪除及管理 Service Fabric Mesh 應用程式。 |
| [node](service-fabric-sfctl-node.md) | 管理形成叢集的節點。 |
| [partition](service-fabric-sfctl-partition.md) | 查詢和管理任何服務的資料分割。 |
| [property](service-fabric-sfctl-property.md) | 在 Service Fabric 名稱底下儲存和查詢屬性。 |
| [replica](service-fabric-sfctl-replica.md) | 管理屬於服務分割區的複本。 |
| [rpm](service-fabric-sfctl-rpm.md) | 查詢命令，並將其傳送至修復管理員服務。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | 管理獨立 Service Fabric 叢集。 |
| [service](service-fabric-sfctl-service.md) | 建立、刪除與管理服務、服務類型和服務套件。 |
| [設定](service-fabric-sfctl-settings.md) | 在 sfctl 的此執行個體設定本機設定。 |
| [store](service-fabric-sfctl-store.md) | 在叢集映像存放區上執行基本檔案層級作業。 |

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。