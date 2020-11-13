---
title: 常見問題-SAP 解決方案的 Azure 監視器 |Microsoft Docs
description: 在本文中，您將瞭解有關 SAP 解決方案 Azure 監視器 (常見問題) 常見問題的解答。
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f872264d5f7bdf4abded516412fe55c417a92e1e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566107"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>SAP 解決方案的 Azure 監視器常見問題 (preview) 
## <a name="frequently-asked-questions"></a>常見問題集

本文提供有關 SAP 解決方案 Azure 監視器 (常見問題) 常見問題的解答。  

 - **我必須支付 SAP 解決方案的 Azure 監視器費用嗎？**  
SAP 解決方案的 Azure 監視器沒有任何授權費用。  
不過，客戶必須負責承擔受控資源群組元件的成本。  

 - **這項服務有哪些區域可供公開預覽？**  
若為公開預覽，這項服務將在美國東部2、美國西部2、美國東部和西歐推出。  

 - **我是否需要提供許可權，以允許在訂用帳戶中部署受控資源群組？**  
否，不需要明確的許可權。  

 - **收集器 VM 位於哪裡？**  
在部署 SAP 解決方案資源的 Azure 監視器時，建議客戶選擇相同的 VNET 來監視資源，作為其 SAP Hana 伺服器。 因此，收集器 VM 建議與 SAP Hana server 位於相同的 VNET 中。 如果客戶使用非 HANA 資料庫，則收集器 VM 將位於與非 HANA 資料庫相同的 VNET 中。  

 - **支援哪些版本的 HANA？**  
HANA 1.0 SPS 12 (Rev 120 或更高版本) 和 HANA 2.0 SPS03 或更高版本  

 - **支援哪些 HANA 部署設定？**  
不支援下列設定：
   - 單一節點 (向上延展) 和多節點 (相應放大)   
   - 單一資料庫容器 (HANA 1.0 SPS 12) 和多個資料庫容器 (HANA 1.0 SPS 12 或 HANA 2.0)   
   - 自動主機容錯移轉 (n + 1) 和 HSR  

 - **支援哪些 SQL Server 版本？**  
SQL Server 2012 SP4 或更新版本。  

 - **支援哪些 SQL Server 設定？**  
不支援下列設定：
   - 虛擬機器中的預設或命名獨立實例  
   - 當您使用叢集資源的虛擬名稱或 AlwaysOn 接聽程式名稱時，AlwaysOn 設定中的叢集實例或實例。 目前不會收集叢集或 AlwaysOn 特定計量    
   - 目前不支援 Azure SQL Database (PAAS)   

 - **如果我不小心刪除受控資源群組，會發生什麼事？**  
受控資源群組預設為鎖定。 因此，客戶的意外刪除受控資源群組的機會是 minuscule。  
如果客戶刪除受控資源群組，SAP 解決方案的 Azure 監視器將會停止運作。 客戶必須為 SAP 解決方案資源部署新的 Azure 監視器，然後重新開始。  

 - **我的 Azure 訂用帳戶中需要哪些角色才能部署 SAP 解決方案資源的 Azure 監視器？**  
參與者角色。  

 - **此產品的 SLA 為何？**  
服務等級協定會排除預覽。 請參閱 SAP 解決方案 marketplace 映射 Azure 監視器的完整授權條款。  

 - **我可以透過這個解決方案監視整個環境嗎？**  
您目前可以監視 HANA 資料庫、基礎結構、高可用性叢集，以及 Microsoft SQL server 的公開預覽。  

 - **這種服務會取代 SAP Solution manager 嗎？**  
不正確。 客戶仍然可以使用 SAP Solution manager 進行商務程式監視。  

 - **這項服務的價值是傳統解決方案，例如 SAP Hana 環境/Studio 嗎？**  
適用于 SAP 解決方案的 Azure 監視器不是 HANA 資料庫專屬的。 SAP 解決方案的 Azure 監視器也支援 AnyDB。  

## <a name="next-steps"></a>後續步驟

- 為 SAP 解決方案資源建立您的第一個 Azure 監視器。
