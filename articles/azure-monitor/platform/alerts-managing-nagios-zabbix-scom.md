---
title: 在 Azure 監視器中管理來自系統中心操作管理器、紮比克斯和納吉奧的警報
description: 在 Azure 監視器中管理來自系統中心操作管理器、紮比克斯和納吉奧的警報
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667443"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>在 Azure 監視器中管理來自系統中心操作管理器、紮比克斯和納吉奧的警報

您現在可以在 [Azure 監視器](https://aka.ms/azure-alerts-overview)中檢視來自 Nagios、Zabbix 和 System Center Operations Manager 的警示。 這些警示來自 Nagios/Zabbix 伺服器或 System Center Operations Manager 整合到 Log Analytics 中。 

## <a name="prerequisites"></a>Prerequisites
Log Analytics 存放庫中具有警示類型的任何記錄，將匯入到 Azure 監視器，所以您必須執行必要組態以收集這些記錄。
1. 對於**Nagios**和**Zabbix**警報，[將這些伺服器配置為](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)向日志分析[發送警報](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json)。
1. 對於**系統中心操作管理器**警報，[請將操作管理器管理組連接到日誌分析工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)。 在此之後，從 Azure 解決方案市集部署[警示管理](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution)解決方案。 完成後，在 System Center Operations Manager 中建立的任何警示會匯入至記錄分析。

## <a name="view-your-alert-instances"></a>檢視警示執行個體
在設定將警示匯入到 Log Analytics 之後，您便可以開始在 [Azure 監視器](https://aka.ms/azure-alerts-overview)中檢視來自這些監視服務的警示執行個體。 一旦這些警示出現在 Azure 監視器中，您即可[管理您的警示執行個體](https://aka.ms/managing-alert-instances)、[管理在這些警示上建立的智慧群組](https://aka.ms/managing-smart-groups)以及[變更警示和智慧群組的狀態](https://aka.ms/managing-alert-smart-group-states)。

> [!NOTE]
>  1. 此解決方案僅允許您在 Azure 監視器中查看系統中心操作管理器/Zabbix/Nagios 觸發警報實例。 警示規則設定則只能在個別的監視工具中檢視/編輯。 
>  1. 所有引發的警示執行個體均可在 Azure 監視器和 Azure Log Analytics 中看到。 目前沒有任何方法可供您在兩者之間做選擇，也無法僅擷取所引發的特定警示。
>  1. 來自系統中心操作管理器、Zabbix 和 Nagios 的所有警報都具有信號類型"未知"，因為基礎遙測類型不可用。
>  1. Nagios 警示不具狀態 – 例如，警示的[監視條件](https://aka.ms/azure-alerts-overview)將不會從「已引發」變更為「已解決」。 相反地，「已引發」和「已解決」兩者會顯示為個別的警示執行個體。 

