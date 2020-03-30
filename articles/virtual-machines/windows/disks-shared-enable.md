---
title: 為 Azure 託管磁片啟用共用磁片
description: 使用共用磁片（預覽）配置 Azure 託管磁片，以便跨多個 VM 共用磁片
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471671"
---
# <a name="enable-shared-disk"></a>啟用共用磁片

本文介紹如何為 Azure 託管磁片啟用共用磁片（預覽）功能。 Azure 共用磁片（預覽）是 Azure 託管磁片的新功能，使您能夠同時將託管磁片附加到多個虛擬機器 （VM）。 通過將託管磁片附加到多個 VM，可以部署新的群集應用程式或將現有群集應用程式遷移到 Azure。 

如果要查找已啟用共用磁片的託管磁片上的概念資訊，請參閱[Azure 共用磁片](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]