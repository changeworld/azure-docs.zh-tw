---
title: 為 Azure 託管磁碟啟用共用磁碟
description: 使用共用磁碟(預覽)設定 Azure 託管磁碟,以便跨多個 VM 共用磁碟
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dbb1844d4c670abfdc5562580b0ee8b4549b6bd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008321"
---
# <a name="enable-shared-disk"></a>開啟分享磁碟

本文介紹如何為 Azure 託管磁碟啟用共用磁碟(預覽)功能。 Azure 共用磁碟(預覽)是 Azure 託管磁碟的新功能,使您能夠同時將託管磁碟附加到多個虛擬機 (VM)。 通過將託管磁碟附加到多個 VM,可以部署新的群集應用程式或將現有群集應用程式遷移到 Azure。 

如果要尋找已開啟分享磁碟的託管磁碟上的概念資訊,請參考[Azure 共用磁碟](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]