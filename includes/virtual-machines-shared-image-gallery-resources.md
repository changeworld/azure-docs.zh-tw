---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788978"
---
| 資源 | 描述|
|----------|------------|
| **映像來源** | 這是資源，可用來在映像庫中建立**映像版本**。 映像來源可以是現有的 Azure VM，其為[一般化或特殊化](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images)、受控映像、快照集，或另一個映像庫中的映像版本。 |
| **映像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像定義會在資源庫內建立，並帶有映像資訊以及在內部使用時所需滿足的需求。 這包括映像是 Windows 還是 Linux、版本資訊以及最小和最大的記憶體需求。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |