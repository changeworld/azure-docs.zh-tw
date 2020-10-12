---
title: 搭配使用 Windows 虛擬桌面與 Azure NetApp Files |Microsoft Docs
description: 提供使用 Azure NetApp Files 部署 Windows 虛擬桌面的最佳作法指引和範例藍圖。
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
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a003090fd610f2ac75895cccbf97750adbd4cfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88258328"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>搭配使用 Azure NetApp Files 和 Windows 虛擬桌面的優點 

本文提供使用 Azure NetApp Files 部署 Windows 虛擬桌面 (WVD) 的最佳作法指引。

Azure NetApp Files 是 Azure 中高效能的檔案儲存體服務。 它可提供高達 450000 IOPS 和毫秒的延遲，能夠支援極大規模的 Windows 虛擬桌面部署。 您可以立即調整頻寬並變更 Azure NetApp Files 磁片區的服務層級，而不需要暫停 IO，同時保留資料平面存取。 這項功能可讓您輕鬆地針對成本優化 WVD 部署規模。 您也可以在不影響磁片區效能的情況下，建立節省空間的時間點磁片區快照集。 這項功能可讓您透過目錄中的複本來復原個別的 [FSLogix 使用者設定檔容器](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile) `~snapshot` ，或透過磁片區還原功能立即立即復原整個磁片區。  有了最多 255 (旋轉) 快照集，以保護磁片區免于資料遺失或損毀，系統管理員有很多機會復原已完成的工作。

## <a name="sample-blueprints"></a>範例藍圖

下列範例藍圖顯示 Windows 虛擬桌面與 Azure NetApp Files 的整合。 在共用的桌面案例中，會使用[多會話虛擬機器](https://docs.microsoft.com/azure/virtual-desktop/windows-10-multisession-faq#what-is-windows-10-enterprise-multi-session)，將使用者導向至集區中 ([廣度優先模式](https://docs.microsoft.com/azure/virtual-desktop/host-pool-load-balancing#breadth-first-load-balancing-method)) 主機的最佳可用會話。 另一方面，在每位使用者都有自己的虛擬機器的情況下，會保留個人桌面。

### <a name="pooled-desktop-scenario"></a>共置桌面案例

針對集區案例，Windows 虛擬桌面團隊 [建議](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) 將下列指引依使用者計數進行 vCPU。 請注意，在此建議中不會指定任何虛擬機器大小。

|     工作負載類型     |     淺色    |     中    |     大量    |
|-----------------------|--------------|---------------|--------------|
|     每個 vCPU 的使用者    |     6        |     4         |     2        |


這項建議是由 500-使用者 LoginVSI 測試確認，並將大約 62 "知識/中型使用者" 記錄到每部 D16as_V4 的虛擬機器。 

例如，在每 D16as_V4 虛擬機器的62使用者中，Azure NetApp Files 可輕鬆地支援每個環境的60000使用者。 測試以評估 D32as_v4 虛擬機器的上限正在進行中。 如果 D32as_v4 的每個 vCPU 建議值都是 true，則超過120000的使用者在 broaching [1000 IP VNet 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)之前，將符合1000部虛擬機器，如下圖所示。  

![Windows 虛擬桌面集區桌面案例](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>個人桌面案例 

在個人桌面案例中，下圖顯示一般用途的架構建議。 使用者會對應到特定的桌面 pod，而且每個 pod 都在1000虛擬機器的位置，而保留空間給從管理 VNet 傳播的 IP 位址。 Azure NetApp Files 可以輕鬆地處理每個單一工作階段主機集區 VNet 的 900 + 個人桌面，而且實際的虛擬機器數目等於1000減去中樞 VNet 中找到的管理主機數目。 如果需要更多個人桌面，可輕鬆地 (主機集區和虛擬網路新增更多 pod) ，如下圖所示。 

![Windows 虛擬桌面個人桌面案例](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

建立像這樣的 POD 型架構時，在登入時將使用者指派給正確的 pod 很重要，因為這可確保使用者一律會找到其使用者設定檔。 

## <a name="next-steps"></a>後續步驟

- [使用 Azure NetApp Files 的方案架構](azure-netapp-files-solution-architectures.md)
