---
title: '設定 Azure 監視器代理程式 (預覽的資料收集) '
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 3cd2ed692f3a34223675da69efd92e78c2ba9504
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082985"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>設定 Azure 監視器代理程式 (預覽的資料收集) 
資料收集規則 (DCR) 定義傳入 Azure 監視器的資料，並指定應傳送的位置。 本文說明如何使用 Azure 監視器代理程式來建立資料收集規則，以從虛擬機器收集資料。

如需資料收集規則的完整描述，請參閱[Azure 監視器 (preview) 中的資料收集規則](data-collection-rule-overview.md)。

> [!NOTE]
> 本文說明如何使用目前為預覽狀態的 Azure 監視器代理程式來設定虛擬機器的資料。 如需正式運作之代理程式的說明，以及如何使用它們來收集資料，請參閱[Azure 監視器代理](agents-overview.md)程式的總覽。


## <a name="dcr-associations"></a>DCR 關聯
若要將 DCR 套用到虛擬機器，請建立虛擬機器的關聯。 虛擬機器可能與多個 Dcr 關聯，而且 DCR 可能會有多個相關聯的虛擬機器。 這可讓您定義一組 Dcr，每個都符合特定需求，並只套用至其適用的虛擬機器。 

例如，假設環境中有一組執行企業營運應用程式的虛擬機器，另一部執行 SQL Server。 您可能會有一個套用到所有虛擬機器的預設資料集合規則，以及個別收集資料給企業營運應用程式和 SQL Server 的資料收集規則。 虛擬機器與資料集合規則的關聯看起來會類似下圖。

![關聯](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>使用 Azure 入口網站建立
您可以使用 Azure 入口網站來建立資料集合規則，並將訂用帳戶中的虛擬機器與該規則產生關聯。 系統會自動安裝 Azure 監視器代理程式，並為尚未安裝它的任何虛擬機器建立受控識別。

在 Azure 入口網站的 [ **Azure 監視器**] 功能表中，從 [**設定**] 區段選取 [**資料收集規則**]。 按一下 **[新增]** 以加入新的資料集合規則和指派。

[![資料收集規則](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

按一下 **[新增]** 以建立新的規則和關聯集。 提供**規則名稱**，並指定**訂**用帳戶和**資源群組**。 這會指定 DCR 的建立位置。 虛擬機器及其關聯可以位於租使用者中的任何訂用帳戶或資源群組中。

[![資料收集規則基本概念](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

在 [**虛擬機器**] 索引標籤中，新增應套用資料收集規則的虛擬機器。 Azure 監視器代理程式將安裝在尚未安裝它的虛擬機器上。

[![資料收集規則虛擬機器](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

在 [**收集並傳遞**] 索引標籤上，按一下 [**新增資料來源**] 來新增資料來源和目的地集。 選取**資料來源類型**，將會顯示所要選取的對應詳細資訊。 針對效能計數器，您可以從一組預先定義的物件及其取樣率進行選取。 對於事件，您可以從一組記錄或設備和嚴重性層級中選取。 

[![資料來源基本](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


若要指定其他記錄檔和效能計數器，請選取 [**自訂**]。 然後，您可以為任何要收集的特定值指定[XPath](https://www.w3schools.com/xml/xpath_syntax.asp) 。 如需範例，請參閱[範例 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 。

[![自訂資料來源](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

在 [**目的地**] 索引標籤上，為數據源加入一個或多個目的地。 Windows 事件和 Syslog 資料來源只能傳送至 Azure 監視器記錄。 效能計數器可以同時傳送至 Azure 監視器計量和 Azure 監視器記錄。

[![Destination](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

按一下 [**新增資料來源**]，然後**查看 [+ 建立**]，以查看資料收集規則的詳細資料，以及與一組 vm 的關聯。 按一下 [**建立**] 加以建立。

> [!NOTE]
> 建立資料收集規則和關聯之後，最多可能需要5分鐘的時間，才會將資料傳送至目的地。


## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 監視器代理程式](azure-monitor-agent-overview.md)。
- 深入瞭解[資料收集規則](data-collection-rule-overview.md)。
