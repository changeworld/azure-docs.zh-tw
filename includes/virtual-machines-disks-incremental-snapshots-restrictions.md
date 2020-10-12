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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102514"
---
- 目前無法在訂用帳戶之間移動增量快照集。
- 您目前最多隻能在任何指定時間產生最多五個特定快照集系列快照集的 SAS Uri。
- 您無法為該磁片訂用帳戶以外的特定磁片建立增量快照集。
- 每個磁片最多可建立7個增量快照集（每隔五分鐘一次）。
- 總共可以為單一磁片建立200增量快照集。
- 您無法在將父磁片的大小變更為 4 TB 的界限之前和之後，取得快照之間的變更。 例如，當磁片的大小為 2 TB 時，您會使用增量快照集快照集。 現在您已將磁片的大小增加到 6 TB，然後採用另一個增量快照集快照集-b。 您無法取得快照集 a 和快照集 b 之間的變更。 您必須再次下載調整大小之後所建立的快照集 b 的完整複本。 接著，您可以在快照集 b 和快照集 b 之後建立的快照集之間取得變更。 
