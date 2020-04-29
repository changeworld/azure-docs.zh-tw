---
title: 商務持續性方案 - QnA Maker
description: 商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887058"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>建立 QnA Maker 服務的商務持續性方案

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

## <a name="business-continuity-with-traffic-manager"></a>流量管理員的業務持續性

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中設定兩個平行的 [QnA Maker 服務](set-up-qnamaker-service-azure.md)。

2. [備份](../../../app-service/manage-backup.md)您的主要 QnA Maker App service，並在次要設定中將它[還原](../../../app-service/web-sites-restore.md)。 這可確保這兩個程式都使用相同的主機名稱和金鑰。

3. 保持主要和次要 Azure 搜尋索引的同步。使用[這裡](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 範例來瞭解如何備份-還原 Azure 索引。

4. 使用[連續匯出](../../../application-insights/app-insights-export-telemetry.md)備份 Application Insights。

5. 設定主要和次要堆疊後，使用[流量管理員](../../../traffic-manager/traffic-manager-overview.md)設定兩個端點，並設定路由方法。

6. 您必須為您的流量管理員端點建立傳輸層安全性（TLS），先前稱為安全通訊端層（SSL）憑證。 系結應用程式服務中[的 TLS/SSL 憑證](../../../app-service/configure-ssl-bindings.md)。

7. 最後，在聊天機器人或應用程式中使用流量管理員端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇容量](./improve-knowledge-base.md)