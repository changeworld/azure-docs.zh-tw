---
title: 搭配使用 Windows 虛擬桌面與 Azure NetApp Files |Microsoft Docs
description: 提供有關使用 Azure NetApp Files 部署 Windows 虛擬桌面的最佳做法指引和範例藍圖。
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
ms.openlocfilehash: 376efe4c66323c9ebbe686e42fe716837b8d8d30
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227071"
---
# <a name="using-windows-virtual-desktop-with-azure-netapp-files"></a>搭配使用 Windows 虛擬桌面與 Azure NetApp Files

本文提供使用 Azure NetApp Files 部署 Windows 虛擬桌面 (WVD) 的最佳做法指引。

Azure NetApp Files 是 Azure 中高效能的檔案儲存體服務。 它最多可提供 450000 IOPS 和毫秒延遲，能夠支援極大規模的 Windows 虛擬桌面部署。 您可以在不暫停 IO 的情況下，隨時視需要調整 Azure NetApp Files 磁片區的頻寬和變更服務等級，同時保留資料平面存取。 這項功能可讓您針對成本輕鬆地優化 WVD 部署規模。 您也可以建立空間有效率的時間點磁片區快照集，而不會影響大量效能。 這項功能可讓您從目錄中的複本復原個別的 [FSLogix 使用者設定檔容器](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile) `~snapshot` ，或透過磁片區還原功能立即復原整個磁片區。  有多達 255 (旋轉) 快照集來保護磁片區免于資料遺失或損毀的情況，系統管理員有很多機會可以復原已完成的作業。

下列範例藍圖顯示 Windows 虛擬桌面與 Azure NetApp Files 的整合。 在集區桌上型電腦案例中，會將使用者導向至最佳的可用會話 (集區中的 [廣度優先模式](https://docs.microsoft.com/azure/virtual-desktop/host-pool-load-balancing#breadth-first-load-balancing-method)) 主機，使用 [多會話虛擬機器](https://docs.microsoft.com/azure/virtual-desktop/windows-10-multisession-faq#what-is-windows-10-enterprise-multi-session)。 另一方面，個人桌面則是保留給每位使用者都有自己的虛擬機器的案例。

## <a name="pooled-desktop-scenario"></a>集區桌面案例

針對集區案例，Windows 虛擬桌面小組會 [建議](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) 將下列使用者計數指引到 vCPU。 請注意，此建議中不會指定任何虛擬機器大小。

|     工作負載類型     |     淺色    |     中型    |     大量    |
|-----------------------|--------------|---------------|--------------|
|     每 vCPU 的使用者    |     6        |     4         |     2        |


這項建議是由 500-使用者 LoginVSI 測試確認，記錄大約62「知識/中型使用者」到每個 D16as_V4 的虛擬機器。 

例如，每個 D16as_V4 虛擬機器的62位使用者，Azure NetApp Files 可以輕鬆地支援每個環境的60000使用者。 正在進行測試以評估 D32as_v4 虛擬機器的上限。 如果 WVD 使用者的每個 vCPU 建議在 D32as_v4 為 true，則在 broaching [1000 IP VNet 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)之前，超過120000個使用者會納入1000部虛擬機器，如下圖所示。  

![Windows 虛擬桌面集區桌面案例](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

## <a name="personal-desktop-scenario"></a>個人桌面案例 

在個人桌面案例中，下圖顯示一般用途的架構建議。 使用者會對應到特定的桌面 pod，而每個 pod 只會在1000虛擬機器下，而保留空間給從管理 VNet 傳播的 IP 位址。 Azure NetApp Files 可以輕鬆處理每個單一工作階段主機集區 VNet 900 個以上的個人桌面電腦，實際的虛擬機器數目等於1000減去中樞 VNet 中找到的管理主機數目。 如果需要更多個人桌面，您可以輕鬆地新增更多 pod (主機集區和虛擬網路) ，如下圖所示。 

![Windows 虛擬桌面個人桌面案例](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

建立這類以 POD 為基礎的架構時，在登入時將使用者指派給正確的 POD，是確保使用者一律會找到其使用者設定檔的重要性。 

## <a name="next-steps"></a>後續步驟

- [使用 Azure NetApp Files 的方案架構](azure-netapp-files-solution-architectures.md)
