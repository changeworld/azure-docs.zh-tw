---
title: 使用 Azure NetApp Files 進行電子設計自動化的優點 |Microsoft Docs
description: 說明 Azure NetApp Files 提供的解決方案，以符合半導體和晶片設計產業的需求。 針對使用 Azure NetApp Files 的電子設計自動化 (EDA) ，提供執行標準產業基準測試的測試案例。
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82160148"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>使用 Azure NetApp Files 進行電子設計自動化的優點

上市 (TTM) 是適用于半導體和晶片設計產業的重要考慮。 業界有高頻寬和低延遲的儲存體需求。 本文說明 Azure NetApp Files 提供來滿足產業需求的解決方案。 它提供的測試案例會針對電子設計自動化 (EDA) 使用 Azure NetApp Files 來執行標準產業基準測試。 

## <a name="test-scenario-configurations"></a>測試案例設定

測試牽涉到三個具有下列設定的案例。 

|    案例    |    磁碟區    |    用戶端<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    一個         |    1          |    1                           |
|    兩個         |    6          |    24                          |
|    三       |    12         |    24                          |

第一個案例可解決單一磁片區的驅動速度。  

第二個和第三個案例會評估單一 Azure NetApp Files 端點的限制。 他們會調查 i/o 上限和延遲的潛在優點。

## <a name="test-scenario-results"></a>測試案例結果

下表摘要說明測試案例的結果。

|    案例       |    I/o 速率<br>  2毫秒     |    I/o 速率<br>  在邊緣     |    Throughput<br>  2毫秒     |    Throughput<br>  在邊緣     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1個磁片區       |    39601                 |    49502                      |    692 MiB/秒                 |    866 MiB/秒                      |
|    6個磁片區      |    255613                |    317000                     |    4577 MiB/秒               |    5568 MiB/秒                    |
|    12個磁片區     |    256612                |    319196                     |    4577 MiB/秒               |    5709 MiB/秒                    |

單一磁片區案例代表基本的應用程式設定。 這是後續測試案例的基準案例。  

六個磁片區案例示範 (600% ) 相對於單一磁片區工作負載的線性增加。  單一虛擬網路內的所有磁片區都是透過單一 IP 位址來存取。  

12個磁片區案例示範在六個磁片區案例中一般降低延遲的情況。 但是，它並沒有對應的提高輸送量。   

下圖說明 Azure NetApp Files 上 EDA 工作負載的延遲和操作速率。  

![Azure NetApp Files 上 EDA 工作負載的延遲和操作率](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

下圖說明 Azure NetApp Files 上 EDA 工作負載的延遲和輸送量。  

![Azure NetApp Files 上 EDA 工作負載的延遲和輸送量](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>測試案例的版面配置 

下表摘要說明測試案例的版面配置。

|    測試案例     |    目錄總數     |    檔案總數     |
|----------------------|------------------------------------|------------------------------|
|    1個磁片區          |    88000                          |    880000                   |
|    6個磁片區         |    568000                         |    5680000                 |
|    12個磁片區        |    568000                         |    5680000                 |

完整的工作負載是同時執行的功能和實體階段的混合。 它代表一組 EDA 工具間的一般流程。   

功能階段是由初始規格和邏輯設計所組成。 當邏輯設計轉換為實體晶片時，就會發生實體階段。 在登出和磁帶輸出階段，最終檢查會完成，而設計則會傳遞至 foundry 進行製造。  

功能性階段包含連續和隨機讀取和寫入 i/o 的混合。 功能性階段是需要大量中繼資料，例如檔案 stat 和存取呼叫。 雖然中繼資料作業實際上沒有大小，但是讀取和寫入作業的範圍介於 1 K 和 16 K 之間。大部分的讀取都介於 4 K 和 16 K 之間。 大部分寫入都是 4 K 或更少。 實體階段是由連續的讀取和寫入作業所組成，混合的是 32 K 和 64 K OP 的大小。  

在上圖中，大部分的輸送量來自于工作負載的連續實體階段。 I/o 來自于小型隨機和中繼資料密集的功能階段。 這兩個階段會以平行方式發生。 

總而言之，您可以將 Azure 計算與適用于 EDA 設計的 Azure NetApp Files 配對，以取得可擴充的頻寬。 

## <a name="next-steps"></a>接下來的步驟

- [使用 Azure NetApp Files 的方案架構](azure-netapp-files-solution-architectures.md)
