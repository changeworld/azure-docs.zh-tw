---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: d14c18ee77662f3803281413f9a844d10d245933
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010992"
---
在 Azure 上，我們提供提升磁片儲存體 IOPS 和 MB/s 效能的能力，亦即虛擬機器和磁片上的負載平衡。 破壞在許多情況下很有用，例如處理非預期的磁片流量或處理批次作業。 您可以有效率地利用 VM 和磁片層級的高載，在您的 VM 和磁片上達成絕佳的基準和高載效能。 如此一來，您就可以在 vm 和磁片上達到絕佳的基準效能和平衡效能。 

請注意，磁片和 Vm 上的高載不會彼此獨立。 如果您有高載磁片，則不需要負載平衡 VM，就能讓磁片進行高載。 如果您有負載平衡的 VM，則不需要高載磁片，即可允許您的 VM 高載。 
