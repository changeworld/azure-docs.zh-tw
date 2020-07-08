---
title: 移動 Azure App Service 資源
description: 使用 Azure Resource Manager 將 App Service 資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80655776"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 資源的移動指引

本文說明移動 App Service 資源的步驟。 將 App Service 資源移到新的訂用帳戶有特定需求。

## <a name="move-across-subscriptions"></a>跨訂用帳戶移動

跨訂用帳戶移動 Web 應用程式時，適用下列指導方針：

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或匯入的 TLS/SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。 請注意，App Service 環境無法移至新的資源群組或新的訂用帳戶。
- 只要憑證與資源群組中的其他所有資源一起移動，您就可以將系結至 web 的憑證移動，而不刪除 TLS 系結。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果 App Service 資源不再位於其原始資源群組中，請將其移回其原始資源群組。 然後，在訂用帳戶之間移動資源。

如果您不記得原始的資源群組，您可以透過診斷來尋找它。 針對您的 web 應用程式，選取 [**診斷並解決問題**]。 然後，選取 [設定**和管理**]。

![選取診斷](./media/app-service-move-limitations/select-diagnostics.png)

選取 [**遷移選項**]。

![選取遷移選項](./media/app-service-move-limitations/select-migration.png)

針對 [建議的步驟] 選取選項，以移動 web 應用程式。

![選取建議的步驟](./media/app-service-move-limitations/recommended-steps.png)

您會在移動資源之前，看到建議採取的動作。 此資訊包括 web 應用程式的原始資源群組。

![建議](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移動支援

若要判斷可以移動的 App Service 資源，請參閱的移動支援狀態：

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。
