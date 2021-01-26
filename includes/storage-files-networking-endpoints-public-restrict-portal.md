---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798542"
---
瀏覽至您想要將其公用端點限制為特定虛擬網路的儲存體帳戶。 在儲存體帳戶的目錄中，選取 [ **網路**]。 

在頁面頂端，選取 [選取的網路] 選項按鈕。 這會將一些設定取消隱藏，以控制公用端點的限制。 按一下 [+ 新增現有虛擬網路]，以選取已允許透過公用端點存取儲存體帳戶的特定虛擬網路。 這將需要選取虛擬網路和該虛擬網路的子網路。 

請核取 [允許受信任的 Microsoft 服務存取此服務帳戶]，以允許受信任的第一方 Microsoft 服務 (例如 Azure 檔案同步) 來存取儲存體帳戶。

[![網路分頁的螢幕擷取畫面，其中具有特定虛擬網路，允許透過公用端點存取儲存體帳戶](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)