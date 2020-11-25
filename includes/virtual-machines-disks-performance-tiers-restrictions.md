---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/20/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 241a2be834d7828cdb56f59313c0c1a9c478ff77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016506"
---
- 這項功能目前僅支援 premium Ssd。
- 您必須從執行中的 VM 解除配置 VM 或卸離您的磁片，然後才能變更磁片層。
- P60、P70 和 P80 效能層級只能由大於 4096 GiB 的磁片使用。
- 磁片的效能層級只能每隔12小時降級一次。