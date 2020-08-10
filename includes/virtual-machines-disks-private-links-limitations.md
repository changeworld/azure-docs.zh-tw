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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420275"
---
- 只能將一個虛擬網路連結到磁碟存取物件。
- 您的虛擬網路必須與磁碟存取物件位於相同的訂用帳戶，才能加以連結。
- 最多可以同時使用相同的磁碟存取物件來匯入或匯出 10 個磁碟或快照集。
- 您無法要求手動核准將虛擬網路連結到磁碟存取物件。
- 與磁碟存取物件相關聯的增量快照集不支援差異功能。
- 您無法使用 AzCopy 來下載透過儲存體帳戶的私人連結保護之磁碟/快照集的 VHD。 不過，您可以使用 AzCopy 將 VHD 下載至您的 VM。
