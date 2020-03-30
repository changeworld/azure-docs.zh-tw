---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173927"
---
接下來，如果叢集上的任何伺服器正在執行 Windows Server 2008 R2 或 Windows Server 2012，您必須確認在屬於叢集一部分的每台內部部署伺服器或 Azure VM 上安裝了 Hotfix [KB2854082](https://support.microsoft.com/kb/2854082) 。 在叢集不在的可用性群組中的任何伺服器或 VM，也應該安裝這個 Hotfix。

在每個叢集節點的遠端桌面工作階段中，下載 [KB2854082](https://support.microsoft.com/kb/2854082) 至本機目錄。 然後，依序在每個叢集節點上安裝 Hotfix。 如果叢集服務目前在叢集節點上執行，伺服器會在 Hotfix 安裝結束時重新啟動。

> [!WARNING]
> 停止叢集服務或重新啟動伺服器會影響叢集和可用性群組的仲裁健全狀況，而且可能造成叢集離線。 若要在安裝期間維護叢集的高可用性，請確定：
> 
> * 叢集處於最佳仲裁健康狀態。 
> * 在任何節點上安裝 Hotfix 之前，所有叢集節點都在線上。
> * 在叢集中的任何其他節點上安裝 Hotfix 之前，允許 Hotfix 安裝在一個節點上執行到完成為止，包括完整重新啟動伺服器。
> 
> 

