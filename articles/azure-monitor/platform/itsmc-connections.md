---
title: Azure 監視器中的 IT 服務管理連接器
description: 本文提供如何將 ITSM 產品/服務與 Azure 監視器中的 IT Service Management Connector (ITSMC) 連線，以集中監視及管理 ITSM 工作項目的相關資訊。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804015"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>將 ITSM 產品/服務與 IT Service Management Connector 連線
本文提供如何設定 ITSM 產品/服務與 Log Analytics 中 IT Service Management Connector (ITSMC) 之間的連線，以集中管理工作項目的相關資訊。 如需 ITSMC 的詳細資訊，請參閱[概觀](./itsmc-overview.md)。

支援下列 ITSM 產品/服務。 選取產品來檢視如何將該產品連線到 ITSMC 的詳細資訊。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> 我們建議 Cherwell 和 Provance 客戶使用 [Webhook 動作](./action-groups.md#webhook) ，以 Cherwell 和 Provance 端點作為整合的另一種解決方案。

## <a name="next-steps"></a>後續步驟

* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)