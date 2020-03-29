---
title: Azure 服務結構 CLI-sfctl
description: 瞭解 sfctl，Azure 服務結構命令列介面。 包括命令和子組的清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906214"
---
# <a name="sfctl"></a>sfctl
用於管理 Service Fabric 叢集和實體的命令。 此版本與 Service Fabric 7.0 運行時相容。

命令會依循「名詞-動詞」模式。 如需詳細資訊，請參閱＜子群組＞。

## <a name="subgroups"></a>子群組
|子群組|描述|
| --- | --- |
| [應用程式](service-fabric-sfctl-application.md) | 建立、刪除與管理應用程式和應用程式類型。 |
| [chaos](service-fabric-sfctl-chaos.md) | 啟動、停止及報告 chaos 測試服務。 |
| [集群](service-fabric-sfctl-cluster.md) | 選取、管理和操作 Service Fabric 叢集。 |
| [組成](service-fabric-sfctl-compose.md) | 建立、刪除和管理 Docker Compose 應用程式。 |
| [容器](service-fabric-sfctl-container.md) | 在叢集節點上執行容器相關命令。 |
| [事件](service-fabric-sfctl-events.md) | 從事件存儲檢索事件（如果已安裝事件存儲服務）。 |
| [is](service-fabric-sfctl-is.md) | 查詢命令，並將其傳送至基礎結構服務。 |
| [mesh](service-fabric-sfctl-mesh.md) | 刪除及管理 Service Fabric Mesh 應用程式。 |
| [節點](service-fabric-sfctl-node.md) | 管理形成叢集的節點。 |
| [分區](service-fabric-sfctl-partition.md) | 查詢和管理任何服務的資料分割。 |
| [財產](service-fabric-sfctl-property.md) | 在 Service Fabric 名稱底下儲存和查詢屬性。 |
| [副本](service-fabric-sfctl-replica.md) | 管理屬於服務分割區的複本。 |
| [rpm](service-fabric-sfctl-rpm.md) | 查詢命令，並將其傳送至修復管理員服務。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | 管理獨立 Service Fabric 叢集。 |
| [服務](service-fabric-sfctl-service.md) | 建立、刪除與管理服務、服務類型和服務套件。 |
| [設定](service-fabric-sfctl-settings.md) | 在 sfctl 的此執行個體設定本機設定。 |
| [存放區](service-fabric-sfctl-store.md) | 在叢集映像存放區上執行基本檔案層級作業。 |

## <a name="next-steps"></a>後續步驟
- [設置](service-fabric-cli.md)服務結構 CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。