---
title: 教學課程：將纜線連接至 Azure FXT Edge Filer
description: 如何為 Azure FXT Edge Filer 硬體進行網路連接埠佈線並連接電源
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84692966"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>教學課程：建立網路連線並為 Azure FXT Edge Filer 節點供電

此教學課程將教導您如何為 Azure FXT Edge Filer 硬體節點的網路連線進行佈線。

在本教學課程中，您將了解： 

> [!div class="checklist"]
> * 如何針對您的環境選擇網路纜線的類型
> * 如何將 Azure FXT Edge Filer 節點連線至您的資料中心網路
> * 如何透過纜線整理支架 (CMA) 配置纜線
> * 如何將電源連接到機架裝置並將其開機

## <a name="prerequisites"></a>Prerequisites

在開始此教學課程之前，Azure FXT Edge Filer 應該安裝在標準配備機架上。 CMA 應安裝在 Filer 節點上。 

## <a name="identify-ports"></a>識別連接埠

識別 Azure FXT Edge Filer 背面的各種連接埠。 
 
![已連接纜線的裝置背面](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>接上裝置纜線

* 如[網路連接埠](#network-ports)中所述，將 RJ-45 連接埠連接到您的資料中心網路來源。  
* 使用安全的 DHCP 伺服器將 [iDRAC 連接埠](#idrac-port)安全地連線至不同的網路。 
* 使用 USB 連接埠和 VGA 連接埠，將鍵盤和顯示器連接到節點以進行初始設定。 您必須啟動節點並[設定初始密碼](fxt-node-password.md)以啟動節點的其他連接埠。 如需詳細資訊，請閱讀[設定初始密碼](fxt-node-password.md)。 

此文章也說明如何為節點[連接 AC 電源](#connect-power-cables)。 

此文章也會說明如何連接到節點的[序列埠](#serial-port-only-when-necessary)，必要時可進行特定疑難排解。 

### <a name="network-ports"></a>網路連接埠 

每個 Azure FXT Edge Filer 節點都包含下列網路連接埠： 

* 六個高速 25GbE/10GbE 雙重速率資料連接埠： 

  * 兩個雙連接埠插入式網路介面卡所提供的四個連接埠
  * 主機板夾層網路介面卡所提供的兩個連接埠 

* 主機板夾層網路介面卡所提供的兩個 1GbE 連接埠 

高速 25GbE/10GbE 資料連接埠具有標準 SFP28 相容機箱。 若要使用光學纜線，您必須安裝 SFP28 光學收發器模組 (未提供)。

1GbE 連接埠具有標準 RJ-45 連接器。

如需支援的完整纜線、交換器及收發器清單，請移至 [Cavium FastlinQ 41000 系列互通性對照表](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) \(英文\)。

針對系統所使用的連線類型取決於您的資料中心環境。

* 如果連線至 25GbE 網路，請使用下列纜線類型的其中一個，為每個高速資料連接埠接上纜線：

  * 光學纜線和 SFP28 光學收發器，具有 25GbE 或雙重速率 25GbE/10GbE 功能
  * SFP28 類型支援 25GbE 直連雙軸纜線

* 如果連線至 10GbE 網路，請使用下列纜線類型的其中一個，為每個高速資料連接埠接上纜線： 

  * 光學纜線和 SFP28 光學收發器，具有 10GbE 或雙重速率 25GbE/10GbE 功能。
  * SFP28 類型支援 25GbE 直連雙軸纜線
  * SFP28 類型支援 10GbE 直連雙軸纜線

* 1GbE 網路連接埠會用於叢集管理流量。 如果要為叢集設定建立實體個別網路 (請參閱[設定管理網路](fxt-cluster-create.md#configure-the-management-network)中所述)，請在建立叢集時核取 [使用 1Gb mgmt 網路]  選項。 使用標準 Cat5 或更好的纜線連接連接埠，如支援的纜線清單中所述。

  如果您打算針對所有流量使用高速連接埠，則可以不連接 1GbE 連接埠。 根據預設，如果有更高速的資料連接埠，則不會使用 1GbE 網路連接埠。  

### <a name="idrac-port"></a>iDRAC 連接埠  

標示為 iDRAC 的連接埠是 1Gb 連線，允許與用於硬體管理和監視的遠端存取控制器進行通訊。 FXT 軟體使用智慧平台管理介面 (IPMI) 搭配此控制器進行疑難排解和修復。 您可以使用內建 [iDRAC 介面](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/)，透過此連接埠監視硬體。 預設會啟用 iDRAC 和 IPMI 存取。 

> [!Note]
> iDRAC 連接埠可以略過作業系統，並直接與節點上的硬體互動。 

連接和設定 iDRAC 連接埠時，請使用下列安全性策略：

* 僅將 iDRAC 連接埠連接到與用來存取叢集的資料網路實際分開的網路。
* 在每個節點上設定安全 iDRAC 系統管理員密碼。 您必須設定此密碼才能啟用硬體 - 請遵循[設定硬體密碼](fxt-node-password.md)中的指示。
* 預設 iDRAC 連接埠設定使用 DHCP 和 IPv4 進行 IP 位址指派。 請確定您的 DHCP 環境受到良好保護，且 DHCP 用戶端和 DHCP 伺服器之間的連線受到限制。 (叢集控制台包含在建立叢集之後變更節點之位址設定方法的設定。)
* 將 iDRAC 連接埠設定為「專用模式」(預設值)，這會將 iDRAC/IPMI 網路流量限制為專用的 RJ-45 連接埠。

iDRAC 連接埠不需要高速網路連線。
  
### <a name="serial-port-only-when-necessary"></a>序列埠 (只在必要時)

在某些情況下，Microsoft 服務及支援中心可能會告訴您將終端機連接到節點的序列埠來診斷問題。  

若要連結主控台：

1. 找到 FXT Edge Filer 節點背面的序列埠 (COM1)。
1. 使用 Null 數據機纜線，將序列埠連接到為 ANSI-115200-8N1 設定的終端機。
1. 登入主控台，並依照支援人員的指示採取其他步驟。

## <a name="route-cables-in-the-cable-management-arm-cma"></a>在纜線整理支架 (CMA) 中配置纜線

每個 Azure FXT Edge Filer 節點都配有選用的纜線整理支架。 CMA 已簡化纜線配置，並且不需要中斷連接纜線即可輕鬆存取機箱背面。 

請遵循下列指示來透過 CMA 配置纜線： 

1. 使用提供的束帶，在纜線進入和離開筐時將纜線捆在一起，這樣它們就不會干擾相鄰的系統 (1)。
1. 在 CMA 處於維修位置時，將捆好的纜線穿過內筐和外筐 (2)。
1. 在筐的任一端使用預先安裝的黏扣帶固定纜線 (3)。
1. 將 CMA 旋轉回托盤上的位置 (4)。
1. 將狀態指示燈纜線安裝在系統背面，並透過將其穿過 CMA 來固定纜線。 將纜線的另一端連接到 CMA 外筐的角落 (5)。 

   > [!CAUTION]
   > 為避免突出纜線可能造成的損害，在將此纜線穿過 CMA 後，請確保狀態指示器纜線中還有彈性空間。 

![已安裝纜線之 CMA 的圖例說明](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  如果您並未安裝 CMA，請使用滑軌套件中提供的兩個黏扣帶，將纜線配置在系統的背面。
> 
>  1. 在兩個機架凸緣的內側找到外部 CMA 托架。
>  2. 輕輕收整纜線，將纜線從系統連接器拉左右兩側。
>  3. 將黏扣帶穿過系統每側外部 CMA 托架的工具式插槽來固定纜線束。
> 
>     ![未使用 CMA 的纜線配置](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>關於 IP 位址需求

針對 Azure FXT Edge Filer 混合式儲存體快取中的硬體節點，IP 位址會由叢集軟體管理。

每個節點至少需要一個 IP 位址，但是在將節點新增至叢集或從叢集中移除節點時會指派節點位址。 

所需的 IP 位址總數取決於構成快取的節點數目。 

安裝節點之後，請使用 [控制台] 軟體設定 IP 位址範圍。 若要深入了解，請閱讀[收集叢集資訊](fxt-cluster-create.md#gather-information-for-the-cluster)。  

## <a name="connect-power-cables"></a>連接電源線

每個 Azure FXT Edge Filer 節點會使用兩個電源供應器 (PSU)。 

> [!TIP] 
> 若要充分利用備援 PSU，請將每個 AC 電源線連接到獨立分支電路上的電源分配器 (PDU)。  
> 
> 您可以使用 UPS 為 PDU 供電以獲得額外保護。 

1. 將附帶的電源線連接至機箱中的 PSU。 請確定電源線和 PSU 完全就位。 
1. 將電源線連接到設備機架上的電源分配器。 可能的話，請為兩條電源線使用兩個不同的電源。 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>開啟 Azure FXT Edge Filer 節點的電源

若要開啟節點電源，請按系統正面的電源按鈕。 按鈕位於右側控制台上。 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>關閉 Azure FXT Edge Filer 節點的電源

電源按鈕可用於在測試期間並將其新增至叢集之前關閉系統。 但是，在將 Azure FXT Edge Filer 節點作為叢集的一部分之後，應使用叢集控制台軟體關閉硬體。 如需詳細資訊，請閱讀[如何安全地關閉 Azure FXT Edge Filer 硬體](fxt-power-off.md)。 

## <a name="next-steps"></a>後續步驟

完成硬體佈線後，開啟每個節點的電源，並透過設定其根密碼對其進行初始化。 
> [!div class="nextstepaction"]
> [設定初始密碼](fxt-node-password.md)
