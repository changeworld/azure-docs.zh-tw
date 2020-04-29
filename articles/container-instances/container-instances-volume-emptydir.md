---
title: 將 emptyDir 磁片區掛接至容器群組
description: 了解如何在 Azure 容器執行個體中掛接 emptyDir 磁碟區，以在容器群組中的容器之間共用資料
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117747"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>在 Azure 容器執行個體中掛接 emptyDir 磁碟區

了解如何在 Azure 容器執行個體中掛接 *emptyDir* 磁碟區，以在容器群組中的容器之間共用資料。 使用*emptyDir*磁片區作為容器化工作負載的暫時快取。

> [!NOTE]
> 目前只有 Linux 容器才能掛接 *emptyDir* 磁碟區。 雖然我們正致力於將所有功能帶入 Windows 容器，但是您可以在[總覽](container-instances-overview.md#linux-and-windows-containers)中找到目前的平臺差異。

## <a name="emptydir-volume"></a>emptyDir 磁碟區

*emptyDir* 磁碟區提供了可供容器群組中每個容器存取的可寫入的目錄。 群組中的容器可以讀取和寫入磁碟區中的相同檔案，且可以在每個容器中使用相同或不同的路徑掛接它。

部分範例會針對 *emptyDir* 磁碟區使用下列項目：

* 塗銷空間
* 長時間執行工作期間的檢查點
* 側車容器所擷取和應用程式容器所提供的儲存資料

*emptyDir* 磁碟區中的資料會透過容器損毀保存。 不過，重新啟動的容器不保證會保存 *emptyDir* 磁碟區中的資料。 如果您停止容器群組， *emptyDir*磁片區不會保存。

Linux *emptyDir*磁片區的大小上限為 50 GB。

## <a name="mount-an-emptydir-volume"></a>掛接 emptyDir 磁碟區

若要在容器實例中掛接 emptyDir 磁片區，您可以使用[Azure Resource Manager 範本](/azure/templates/microsoft.containerinstance/containergroups)、 [YAML](container-instances-reference-yaml.md)檔案或其他程式設計方法來部署容器群組。

首先，在檔案`volumes`的 [容器群組`properties` ] 區段中填入陣列。 接下來，針對您想要掛接 *emptyDir* 磁碟區所在容器群組中的每個容器，填入容器定義之 `properties` 區段中的 `volumeMounts` 陣列。

例如，下列 Resource Manager 範本會建立一個由兩個容器 (個別掛接了 *emptyDir* 磁碟區) 組成的容器群組：

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

若要查看容器群組部署的範例，請參閱[使用 Resource Manager 範本部署多容器群組](container-instances-multi-container-group.md)和[使用 YAML 檔案部署多容器群組](container-instances-multi-container-yaml.md)。

## <a name="next-steps"></a>後續步驟

了解如何在 Azure 容器執行個體中掛接其他類型的磁碟區：

* [在 Azure 容器執行個體中掛接 Azure 檔案共用](container-instances-volume-azure-files.md)
* [在 Azure 容器執行個體中掛接 gitRepo 磁碟區](container-instances-volume-gitrepo.md)
* [在 Azure 容器執行個體中掛接秘密磁碟區](container-instances-volume-secret.md)