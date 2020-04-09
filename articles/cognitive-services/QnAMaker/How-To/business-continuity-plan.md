---
title: 商務持續性方案 - QnA Maker
description: 商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887058"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>建立 QnA Maker 服務的商務持續性方案

商務持續性方案的主要目標是建立彈性的知識庫端點，藉以確保聊天機器人或應用程式使用知識庫時不會停機。

## <a name="business-continuity-with-traffic-manager"></a>與流量管理員的業務連續性

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 方案](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

以上呈現的高階構想如下所示：

1. 在 [Azure 配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中設定兩個平行的 [QnA Maker 服務](set-up-qnamaker-service-azure.md)。

2. [備份](../../../app-service/manage-backup.md)主 QnA Maker 應用服務,並在輔助設置中[還原](../../../app-service/web-sites-restore.md)該服務。 這將確保兩個設置使用相同的主機名和密鑰。

3. 使主 Azure 和輔助 Azure 搜索索引保持同步。使用[此處](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 範例瞭解如何備份還原 Azure 索引。

4. 使用[連續匯出](../../../application-insights/app-insights-export-telemetry.md)備份 Application Insights。

5. 設定主要和次要堆疊後，使用[流量管理員](../../../traffic-manager/traffic-manager-overview.md)設定兩個端點，並設定路由方法。

6. 您需要為流量管理器終結點創建傳輸層安全 (TLS),以前稱為安全套接字層 (SSL)。 在套用服務中[用用 TLS/SSL 憑證](../../../app-service/configure-ssl-bindings.md)。

7. 最後，在聊天機器人或應用程式中使用流量管理員端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇容量](./improve-knowledge-base.md)