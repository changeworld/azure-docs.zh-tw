---
title: 使用共用的 VM 映射，在 Azure CLI 中建立擴展集
description: 了解如何使用 Azure CLI 來建立共用 VM 映像，以用來在 Azure 中部署虛擬機器擴展集。
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 418258c498f768697aefebd5df0cc64b7255076f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676073"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 為虛擬機器擴展集建立及使用共用映像

當您建立擴展集時，您會指定部署 VM 執行個體時所要使用的映像。 [共用映像資源庫](../virtual-machines/shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映射庫可讓您與其他人共用您的映射。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>下一步

從 [VM](../virtual-machines/image-version-vm-cli.md)或 [受控映射](../virtual-machines/image-version-managed-image-cli.md)建立映射版本。

如需共用映像資源庫的詳細資訊，請參閱[概觀](../virtual-machines/shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](../virtual-machines/troubleshooting-shared-images.md)。