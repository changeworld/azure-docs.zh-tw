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
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102514"
---
- 目前無法在訂用帳戶之間移動增量快照集。
- 您目前最多隻能在任何指定的時間產生特定快照集系列的五個快照集的 SAS Uri。
- 您無法為該磁片的訂用帳戶以外的特定磁片建立增量快照集。
- 每個磁片最多可以建立7個增量快照集每五分鐘一次。
- 總共可以針對單一磁片建立總計200的增量快照集。
- 您無法在變更父系磁片大小到 4 TB 界限之間的快照集之間取得變更。 例如，當磁片的大小為 2 TB 時，您會拍攝增量快照集快照集。 現在您已將磁片大小增加至 6 TB，然後採用另一個增量快照集快照集 b。 您無法取得快照集 a 和快照集 b 之間的變更。 您必須在調整大小之後，再次下載建立的快照集 b 完整複本。 之後，您可以取得快照集 b 與快照集 b 之後所建立快照集之間的變更。 
