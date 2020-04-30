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
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204538"
---
- 目前無法在訂用帳戶之間移動增量快照集。
- 您目前最多隻能在任何指定的時間產生特定快照集系列的五個快照集的 SAS Uri。
- 您無法為該磁片的訂用帳戶以外的特定磁片建立增量快照集。
- 每個磁片最多可以建立7個增量快照集每五分鐘一次。
- 總共可以針對單一磁片建立總計200的增量快照集。
- 您無法在跨 4 TB 界限的父系磁片大小變更之前和之後，取得快照之間的變更。 您必須再次下載調整大小之後所建立之快照集的完整複本。 之後，您可以在調整大小的 4 TB 界限之後，取得所建立快照集之間的變更。 
