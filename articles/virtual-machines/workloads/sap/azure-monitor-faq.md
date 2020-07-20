---
title: SAP 解決方案的常見問題-Azure 監視器 |Microsoft Docs
description: 本文提供適用于 SAP 解決方案的 Azure 監視器常見問題的解答
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: cf0366300c4fab18a0f6231a97ca050eddd50132
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852259"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>適用于 SAP 解決方案的 Azure 監視器常見問題（預覽）
## <a name="frequently-asked-questions"></a>常見問題集

本文提供適用于 SAP 解決方案的 Azure 監視器常見問題（FAQ）的解答。  

 - **我必須為 SAP 解決方案的 Azure 監視器付費嗎？**  
SAP 解決方案的 Azure 監視器沒有授權費用。  
不過，客戶須負責承擔受控資源群組元件的成本。  

 - **在哪些區域中，此服務可供公開預覽？**  
若為公開預覽，這項服務將在美國東部2、美國西部2、美國東部和西歐提供使用。  

 - **我是否需要提供許可權，以允許在我的訂用帳戶中部署受控資源群組？**  
否，不需要明確許可權。  

 - **收集器 VM 位於何處？**  
在部署 SAP 解決方案資源的 Azure 監視器時，我們建議客戶選擇相同的 VNET 來監視資源作為其 SAP Hana 伺服器。 因此，建議收集器 VM 位於與 SAP Hana server 相同的 VNET 中。 如果客戶使用非 HANA 資料庫，收集器 VM 將位於與非 HANA 資料庫相同的 VNET 中。  

 - **支援哪些 HANA 版本？**  
HANA 1.0 SPS 12 （修訂版120或更高版本）和 HANA 2.0 SPS03 或更高版本  

 - **支援哪些 HANA 部署設定？**  
不支援下列設定：
   - 單一節點（向上擴充）和多節點（向外延展）  
   - 單一資料庫容器（HANA 1.0 SPS 12）和多個資料庫容器（HANA 1.0 SPS 12 或 HANA 2.0）  
   - 自動主機容錯移轉（n + 1）和 HSR  

 - **支援哪些 SQL Server 版本？**  
SQL Server 2012 SP4 或更新版本。  

 - **支援哪些 SQL Server 設定？**  
不支援下列設定：
   - 虛擬機器中的預設或命名獨立實例  
   - 當使用叢集資源的虛擬名稱或 AlwaysOn 接聽程式名稱時，AlwaysOn 設定中的叢集實例或實例。 目前不會收集任何叢集或 AlwaysOn 特定計量    
   - 目前不支援 Azure SQL Database （PAAS）  

 - **如果我不小心刪除受控資源群組，會發生什麼事？**  
受控資源群組預設為鎖定。 因此，客戶可能會意外刪除受管理的資源群組 minuscule。  
如果客戶刪除受管理的資源群組，SAP 解決方案的 Azure 監視器將會停止運作。 客戶必須為 SAP 解決方案資源部署新的 Azure 監視器，並從頭開始。  

 - **我的 Azure 訂用帳戶中需要哪些角色，才能部署 SAP 解決方案資源的 Azure 監視器？**  
參與者角色。  

 - **此產品的 SLA 為何？**  
預覽會從服務等級協定中排除。 請參閱 SAP 解決方案 marketplace 映射 Azure 監視器的完整授權條款。  

 - **我可以透過這個解決方案來監視整個橫向嗎？**  
您目前可以監視 HANA 資料庫、基礎結構、高可用性叢集，以及 Microsoft SQL server 的公開預覽版本。  

 - **此服務會取代 SAP 解決方案管理員嗎？**  
否。 客戶仍然可以使用 SAP Solution manager 進行商務程式監視。  

 - **這項服務在傳統解決方案（例如 SAP Hana 的考核室/Studio）上的價值為何？**  
SAP 解決方案的 Azure 監視器不是 HANA 資料庫特有的。 SAP 解決方案的 Azure 監視器也支援 AnyDB。  

## <a name="next-steps"></a>後續步驟

- 建立您的第一個 Azure 監視器的 SAP 解決方案資源。