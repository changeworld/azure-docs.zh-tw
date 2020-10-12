---
title: 概念-私用雲端更新和升級
description: 深入瞭解 Azure VMware 解決方案中的重要升級程式和功能。
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 380e97eae559145a9ef5ed7b6e7bf14f18039eed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316795"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware 解決方案私用雲端更新和升級

## <a name="overview"></a>概觀

Azure VMware 解決方案私用雲端的主要優點之一，就是為您維護平臺。 平臺維護包括自動更新 VMware 驗證軟體套件組合，協助確保您使用的是最新版的已驗證 Azure VMware 解決方案私用雲端軟體。

具體而言，Azure VMware 解決方案私用雲端包含：

- 以 VMware ESXi 的虛擬程式布建的專用裸機伺服器節點 
- 用於管理 ESXi 和 vSAN 的 vCenter server 
- VMware NSX-T vSphere 工作負載 Vm 的軟體定義網路  
- 適用于 vSphere 工作負載 Vm 的 VMware vSAN 資料存放區  
- 適用于工作負載行動性的 VMware HCX  

除了這些元件之外，Azure VMware 解決方案私用雲端還包含 Azure 基礎中的資源，以連線及操作私用雲端。 Azure VMware 解決方案會持續監視基礎和 VMware 元件的健康情況。 當 Azure VMware 解決方案偵測到失敗時，會採取動作來修復失敗的元件。 

## <a name="what-components-get-updated"></a>哪些元件會更新？   

Azure VMware 解決方案會更新下列 VMware 元件： 

- 在裸機伺服器節點上執行 vCenter Server 和 ESXi 
- vSAN 
- NSX-T 

Azure VMware 解決方案也會更新基礎中的軟體，例如驅動程式、網路交換器上的軟體，以及裸機節點上的固件。 

## <a name="types-of-updates"></a>更新類型

Azure VMware 解決方案會將下列類型的更新套用至 VMware 元件：

- 修補程式： VMware 所發行的安全性修補程式和 bug 修正。 
- 更新：一或多個 VMware 元件的次要版本更新。 
- 升級：一或多個 VMware 元件的主要版本更新。

在您的私人雲端套用修補程式之前和之後，您將會收到通知。 我們也會與您合作，在將更新或升級套用至您的私人雲端之前，先排程維護時段。 

## <a name="vmware-appliance-backup"></a>VMware 設備備份 

除了進行更新，Azure VMware 解決方案還會針對這些 VMware 元件進行設定備份：

- vCenter Server 
- NSX-T 管理員 

在失敗的時候，Azure VMware 解決方案可以從設定備份還原這些設定。 

如需 VMware 軟體版本的詳細資訊，請參閱私用雲端 [和叢集概念文章](concepts-private-clouds-clusters.md) 和 [常見問題](faq.md)。

## <a name="next-steps"></a>後續步驟

下一步是 [建立私人雲端](tutorial-create-private-cloud.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
