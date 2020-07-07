---
title: 適用于 Linux 的 Azure NetApp Files 效能基準測試 |Microsoft Docs
description: 說明 Azure NetApp Files 為 Linux 提供的效能基準測試。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614586"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>適用于 Linux 的 Azure NetApp Files 效能基準測試

本文說明 Azure NetApp Files 為 Linux 提供的效能基準測試。

## <a name="linux-scale-out"></a>Linux 相應放大

本節說明 Linux 工作負載輸送量和工作負載 IOPS 的效能效能評定。

### <a name="linux-workload-throughput"></a>Linux 工作負載輸送量  

下圖代表 64-kibibyte （KiB）順序工作負載和 1 TiB 的工作集。 它會顯示單一 Azure NetApp Files 磁片區可以在 ~ 1600 MiB/s 純連續寫入和 ~ 4500 MiB/s 純連續讀取之間處理。  

圖形會一次從純讀取到純寫入，以10% 的形式顯示減少。 它會示範使用不同的讀取/寫入比例時，您可以預期的情況（100%：0%、90%：10%、80%：20% 等等）。

![Linux 工作負載輸送量](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 工作負載 IOPS  

下圖代表 4 kibibyte （KiB）隨機工作負載和 1 TiB 的工作集。 此圖表顯示 Azure NetApp Files 磁片區可以在 ~ 130000 純隨機寫入和 ~ 460000 純隨機讀取之間處理。  

此圖說明一次從純讀取到純寫入的10% 減少。 它會示範使用不同的讀取/寫入比例時，您可以預期的情況（100%：0%、90%：10%、80%：20% 等等）。

![Linux 工作負載 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 相應增加  

Linux 5.3 核心啟用適用于 NFS 的單一用戶端向外延展網路- `nconnect` 。 本節中的圖形會顯示使用 NFSv3 之用戶端掛接選項的驗證測試結果。 此功能適用于 SUSE （從 SLES12SP4 開始）和 Ubuntu （從19.10 版開始）。 這在概念上類似于 SMB 多重通道和 Oracle Direct NFS。

圖形會比較 `nconnect` 與非連接的已掛接磁片區的優點。 在圖形中，FIO 會從美國西部 2 Azure 區域中的單一 D32s_v3 實例產生工作負載。

### <a name="linux-read-throughput"></a>Linux 讀取輸送量  

下圖顯示 ~ 3500 MiB/s 讀取的連續讀取 `nconnect` ，大約是 2.3 x 非 `nconnect` 。

![Linux 讀取輸送量](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 寫入輸送量  

下圖顯示順序寫入。 它們表示 `nconnect` 對順序寫入沒有顯著的好處。 1500 MiB/s 大約是連續寫入磁片區的上限和 D32s_v3 實例輸出限制。

![Linux 寫入輸送量](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 讀取 IOPS  

下圖顯示 ~ 200000 讀取 IOPS 的隨機讀取 `nconnect` ，大約3倍非 `nconnect` 。

![Linux 讀取 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 寫入 IOPS  

下圖顯示的是 ~ 135000 寫入 IOPS 的隨機寫入 `nconnect` ，大約3倍非 `nconnect` 。

![Linux 寫入 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>後續步驟

- [Azure NetApp Files：充分運用您的雲端儲存體](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
