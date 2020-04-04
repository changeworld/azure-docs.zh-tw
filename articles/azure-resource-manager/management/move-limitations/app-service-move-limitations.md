---
title: 移動 Azure 應用服務資源
description: 使用 Azure 資源管理員將應用服務資源移動到新資源組或訂閱。
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655776"
---
# <a name="move-guidance-for-app-service-resources"></a>移動應用服務資源的指南

本文介紹了移動應用服務資源的步驟。 將應用服務資源移動到新訂閱有特定要求。

## <a name="move-across-subscriptions"></a>跨訂閱移動

跨訂閱行動 Web 應用時,以下指南適用:

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或匯入的 TLS/SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。 請注意,應用服務環境不能移動到新的資源組,也不能移動到新的訂閱。
- 只要證書與資源組中的所有其他資源一起移動,就可以在不刪除 TLS 綁定的情況下行動綁定到 Web 的證書。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果應用服務資源不再位於其原始資源組中,則將其移回其原始資源組。 然後,跨訂閱移動資源。

如果您不記得原始資源組,可以通過診斷找到它。 對於 Web 應用,選擇 **「診斷並解決問題**」。 然後,選擇**設定與管理**。

![選擇診斷](./media/app-service-move-limitations/select-diagnostics.png)

選擇**要移動選項**。

![選擇移轉選項](./media/app-service-move-limitations/select-migration.png)

選擇建議的步驟以行動 Web 應用的選項。

![選擇建議的步驟](./media/app-service-move-limitations/recommended-steps.png)

在移動資源之前,您將看到要執行的操作。 該資訊包括 Web 應用的原始資源組。

![建議](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移動支援

要確定可以移動哪些應用服務資源,請參閱移動支援狀態:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。
