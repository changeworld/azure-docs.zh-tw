---
title: 輸入/輸出 IP 位址
description: 瞭解輸入和輸出 IP 位址在 Azure App Service 中的使用方式、變更時間，以及如何尋找您的應用程式位址。
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8bcd80fde95e467513590f3ed09b1dadd2646aee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537622"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service 中的輸入和輸出 IP 位址

[Azure App Service](overview.md) 是多租用戶服務，但 [App Service 環境](environment/intro.md)除外。 不在 App Service 環境中的應用程式 (不在[隔離層](https://azure.microsoft.com/pricing/details/app-service/)) 會與其他應用程式共用網路基礎結構。 因此，應用程式的輸入和輸出 IP 位址可能不同，甚至會在某些情況下變更。 

[App Service 環境](environment/intro.md)使用專用的網路基礎結構，因此在 App Service 環境中執行的應用程式會針對輸入和輸出連線取得靜態的專用 IP 位址。

## <a name="when-inbound-ip-changes"></a>當輸入 IP 變更時

不論相應放大的執行個體數目為何，每個應用程式都只有一個輸入 IP 位址。 輸入 IP 位址可能會在您執行下列動作之一時變更：

- 刪除應用程式，並在不同資源群組中重建。
- 刪除資源群組和__ 區域組合中的最後一個應用程式，並予以重建。
- 刪除現有的 TLS 系結，例如在憑證更新期間（請參閱[更新憑證](configure-ssl-certificate.md#renew-certificate)）。

## <a name="find-the-inbound-ip"></a>尋找輸入 IP

只要在本機終端機中執行下列命令：

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>取得靜態的輸入 IP

有時候您可能會想讓應用程式使用專用的靜態 IP 位址。 若要取得靜態的輸入 IP 位址，您必須[保護自訂網域](configure-ssl-bindings.md#secure-a-custom-domain)。 如果您實際上不需要 TLS 功能來保護您的應用程式，您甚至可以上傳此系結的自我簽署憑證。 在以 IP 為基礎的 TLS 系結中，憑證會系結至 IP 位址本身，因此 App Service 會布建靜態 IP 位址來進行這項操作。 

## <a name="when-outbound-ips-change"></a>當輸出 IP 變更時

不論相應放大的執行個體數目為何，每個應用程式在任何給定時間都會有一定數目的輸出 IP 位址。 任何來自 App Service 應用程式的輸出連線 (例如連往後端資料庫) 都會使用其中一個輸出 IP 位址來作為來源 IP 位址。 您無法事先知道給定的應用程式執行個體會使用哪個 IP 位址來進行輸出連線，因此您的後端服務必須對應用程式的所有輸出 IP 位址開啟其防火牆。

當您在較低層級 (**基本**、**標準**和**進階**) 和**進階 V2** 層級之間調整應用程式時，應用程式的該組輸出 IP 位址會變更。

您可以在 Azure 入口網站**的 [內容**] 分頁中，尋找`possibleOutboundIpAddresses`屬性或 [**其他輸出 ip 位址**] 欄位，以尋找您的應用程式可以使用的所有可能輸出 ip 位址集合（不論定價層為何）。 請參閱[尋找輸出 IP](#find-outbound-ips)。

## <a name="find-outbound-ips"></a>尋找輸出 IP

若要在 Azure 入口網站中尋找應用程式目前所使用的輸出 IP 位址，請按一下應用程式左側導覽中的 [屬性]****。 它們會列在 [**輸出 IP 位址**] 欄位中。

您可以在 [Cloud Shell](../cloud-shell/quickstart.md) 中執行下列命令來找到同樣的資訊。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

若要尋找應用程式的_所有_可能輸出 IP 位址，不論定價層為何，請按一下應用程式左側導覽中的 [**屬性**]。 它們會列在 [**其他輸出 IP 位址**] 欄位中。

您可以在 [Cloud Shell](../cloud-shell/quickstart.md) 中執行下列命令來找到同樣的資訊。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>後續步驟

了解如何依來源 IP 位址限制輸入流量。

> [!div class="nextstepaction"]
> [靜態 IP 限制](app-service-ip-restrictions.md)
