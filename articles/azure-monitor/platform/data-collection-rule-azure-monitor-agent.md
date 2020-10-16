---
title: '設定 Azure 監視器代理程式 (預覽的資料收集) '
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: cd29bfafe2d37b6a34031e6962cc27bfff0006c1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108009"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>設定 Azure 監視器代理程式 (預覽的資料收集) 
資料收集規則 (DCR) 定義進入 Azure 監視器的資料，並指定應該傳送的位置。 本文說明如何使用 Azure 監視器代理程式建立資料收集規則，以收集虛擬機器的資料。

如需資料收集規則的完整說明，請參閱 [Azure 監視器 (preview) 中的資料集合規則 ](data-collection-rule-overview.md)。

> [!NOTE]
> 本文說明如何使用目前為預覽狀態的 Azure 監視器代理程式來設定虛擬機器的資料。 請參閱 [Azure 監視器代理](agents-overview.md) 程式的總覽，以取得已正式運作之代理程式的描述，以及如何使用它們來收集資料。


## <a name="dcr-associations"></a>DCR 關聯
若要將 DCR 套用至虛擬機器，您可以建立虛擬機器的關聯。 虛擬機器可能會有多個 Dcr 的關聯，而 DCR 可能會有多個相關聯的虛擬機器。 這可讓您定義一組 Dcr，每個都符合特定的需求，並只將它們套用至它們適用的虛擬機器。 

例如，假設有一組虛擬機器的環境，執行企業營運應用程式和其他執行 SQL Server 的虛擬機器。 您可能會有一個預設的資料集合規則適用于所有虛擬機器，以及個別的資料收集規則，這些規則會特別針對企業營運應用程式和 SQL Server 收集資料。 虛擬機器與資料集合規則的關聯看起來會如下圖所示。

![下圖顯示裝載企業營運應用程式和 SQL Server 的虛擬機器，這些虛擬機器會與名為 central 的資料集合規則相關聯（例如，適用于企業營運應用程式的預設和 lob 應用程式），以及用於 SQL Server 的中央-i t 預設和 s q l。](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>使用 Azure 入口網站建立
您可以使用 Azure 入口網站來建立資料集合規則，並將您訂用帳戶中的虛擬機器與該規則建立關聯。 系統會自動安裝 Azure 監視器代理程式，並針對尚未安裝它的任何虛擬機器建立受控識別。

在 Azure 入口網站的 [ **Azure 監視器**] 功能表中，從 [**設定**] 區段選取 [**資料收集規則**]。 按一下 **[新增]** ，以加入新的資料收集規則和指派。

[![資料收集規則](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

按一下 [ **新增** ] 以建立新規則和關聯集。 提供 **規則名稱** 並指定 **訂** 用帳戶和 **資源群組**。 這會指定建立 DCR 的位置。 虛擬機器和其關聯可以位於租使用者中的任何訂用帳戶或資源群組中。

[![資料收集規則基本概念](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

在 [ **虛擬機器** ] 索引標籤中，新增應套用資料收集規則的虛擬機器。 Azure 監視器代理程式將會安裝在尚未安裝它的虛擬機器上。

[![資料收集規則虛擬機器](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

在 [ **收集和傳遞** ] 索引標籤上，按一下 [ **加入資料來源** ] 以加入資料來源和目的地集合。 選取 **資料來源類型**，就會顯示所要選取的對應詳細資料。 針對效能計數器，您可以從一組預先定義的物件和其取樣率進行選取。 對於事件，您可以從一組記錄或設備和嚴重性層級進行選取。 

[![資料來源基本](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


若要指定其他記錄和效能計數器，請選取 [ **自訂**]。 然後，您可以指定要收集之任何特定值的 [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) 。 如需範例，請參閱 [範例 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 。

[![資料來源自訂](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

在 [ **目的地** ] 索引標籤上，新增資料來源的一或多個目的地。 Windows 事件和 Syslog 資料來源只能傳送至 Azure 監視器記錄。 效能計數器可以同時傳送至 Azure 監視器計量和 Azure 監視器記錄。

[![Destination](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

按一下 [ **新增資料來源** ]，然後 **查看 [+ 建立** ]，以檢查資料收集規則的詳細資料，並與一組 vm 建立關聯。 按一下 [ **建立** ] 來建立它。

> [!NOTE]
> 一旦建立資料收集規則和關聯之後，最多可能需要5分鐘的時間，資料才會傳送至目的地。

## <a name="createusingrestapi"></a>使用 REST API 建立
遵循下列步驟，使用 REST API 建立 DCR 和關聯。 
1.使用 [範例 DCR](data-collection-rule-overview.md#sample-data-collection-rule)中所示的 JSON 格式，手動建立 DCR 檔案。
2.使用 [REST API](/rest/api/monitor/datacollectionrules/create#examples)建立規則。
3.使用 [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)為每個虛擬機器建立資料集合規則的關聯。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 監視器代理程式](azure-monitor-agent-overview.md)。
- 深入瞭解 [資料收集規則](data-collection-rule-overview.md)。