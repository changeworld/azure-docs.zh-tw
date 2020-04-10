---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008621"
---
目前,超磁碟有額外的限制,如下所示:

目前唯一可用於超磁碟的基礎結構冗餘選項是可用性區域。 使用任何其他冗餘選項的 VM 無法連接超磁碟。

下表概述了提供區域超磁碟的區域及其相應的可用性選項:

> [!NOTE]
> 這些區域中的某些可用性區域不提供超磁碟。

|區域  |無基礎架構冗餘  |可用性區域  |
|---------|---------|---------|
|美國西部     |是         |否         |
|美國西部 2    |否         |是         |
|美國東部     |否         |是         |
|美國東部 2     |否         |是         |
|東南亞     |否         |是         |
|北歐     |否         |是         |
|西歐     |否         |是         |
|英國南部     |否         |是         |

- 只在以下 VM 系列中受支援:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非所有 VM 大小都可用於每個支援的區域,具有超磁碟
- 僅作為數據磁碟提供,僅支援 4k 物理扇區大小。 由於超磁碟的 4K 本機扇區大小,有些應用程式與超磁碟不相容。 例如,Oracle 資料庫需要版本 12.2 或更高版本才能支援超磁碟。  
- 只能建立為空磁碟  
- 目前不支援磁碟快照、VM 映像、可用性集、Azure 專用主機或 Azure 磁碟加密
- 目前不支援與 Azure 備份或 Azure 網站恢復整合
- GA VM 上的 IOPS 當前最大限制為 80,000。

默認情況下,Azure 超級磁碟每個訂閱最多提供 16 個 TiB,但超磁碟可按請求支援更高的容量。 要請求增加容量,請與 Azure 支援部門聯繫。