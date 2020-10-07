---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377494"
---
- 只能將一個虛擬網路連結到磁碟存取物件。
- 您的虛擬網路必須與磁碟存取物件位於相同的訂用帳戶，才能加以連結。
- 最多可以同時使用相同的磁碟存取物件來匯入或匯出 10 個磁碟或快照集。
- 您無法要求手動核准將虛擬網路連結到磁碟存取物件。
- 累加快照集與磁碟存取物件相關聯時無法匯出。
- 您無法使用 AzCopy 來下載透過儲存體帳戶的私人連結保護之磁碟/快照集的 VHD。 不過，您可以使用 AzCopy 將 VHD 下載至您的 VM。
