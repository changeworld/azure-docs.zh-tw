---
title: IT 服務管理連接器-Azure 監視器中使用 ServiceNow 進行安全匯出
description: 本文說明如何在 Azure 監視器的安全匯出中，將您的 ITSM 產品/服務與 ServiceNow 連接在一起。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839402"
---
# <a name="connect-servicenow-to-azure-monitor"></a>將 ServiceNow 連線到 Azure 監視器

下列各節提供有關如何在 Azure 中連接 ServiceNow 產品和保護匯出的詳細資料。

## <a name="prerequisites"></a>必要條件

確定您符合下列必要條件：

* Azure AD 已註冊。
* 您有支援的 ServiceNow 事件管理版本-ITOM (奧蘭多版或更新版本) 。

## <a name="configure-the-servicenow-connection"></a>設定 ServiceNow 連接

1. 使用連結 HTTPs:// (實例名稱) . service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor 安全匯出定義的 URI。

2. 依照下列版本的指示進行：
   * [巴黎](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [奧蘭多](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [紐約](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)