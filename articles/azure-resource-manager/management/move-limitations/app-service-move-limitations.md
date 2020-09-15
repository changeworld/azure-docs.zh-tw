---
title: 移動 Azure App Service 資源
description: 使用 Azure Resource Manager 將 App Service 資源移至新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531367"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 資源的移動指導方針

本文說明移動 App Service 資源的步驟。 將 App Service 資源移至新的訂用帳戶有特定需求。

## <a name="move-across-subscriptions"></a>跨訂用帳戶移動

跨訂用帳戶移動 Web 應用程式時，適用下列指導方針：

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或已匯入的 TLS/SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。
- App Service 環境無法移至新的資源群組或訂用帳戶。 不過，您可以將 web 應用程式和 app service 方案移至新的訂用帳戶，而不需要移動 App Service 環境。 移動之後，web 應用程式就不會再裝載于 App Service 環境中。
- 只要將憑證與資源群組中的其他所有資源一起移動，您就可以在不刪除 TLS 系結的情況下移動系結至網路的憑證。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果 App Service 資源不再位於其原始資源群組中，請將其移回其原始資源群組。 然後，在訂用帳戶之間移動資源。

如果您不記得原始資源群組，您可以透過診斷來尋找它。 針對您的 web 應用程式，選取 [ **診斷並解決問題**]。 然後，選取 [設定 **及管理**]。

![選取診斷](./media/app-service-move-limitations/select-diagnostics.png)

選取 [ **遷移選項**]。

![選取遷移選項](./media/app-service-move-limitations/select-migration.png)

針對移動 web 應用程式的建議步驟，選取選項。

![選取建議的步驟](./media/app-service-move-limitations/recommended-steps.png)

您會在移動資源之前，看到建議採取的動作。 此資訊包括 web 應用程式的原始資源群組。

![螢幕擷取畫面顯示移動 Microsoft 點 Web 資源的建議步驟。](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移動支援

若要判斷哪些 App Service 資源可以移動，請參閱移動支援狀態：

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。
