---
title: 輸入/輸出 IP 位址
description: 瞭解如何在 Azure App Service 中使用輸入和輸出 IP 位址（當它們變更時），以及如何尋找您的應用程式位址。
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e5b271cc5cd8cb52267b6ee44bc3965d0e4b0aab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746148"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service 中的輸入和輸出 IP 位址

[Azure App Service](overview.md) 是多租用戶服務，但 [App Service 環境](environment/intro.md)除外。 不在 App Service 環境中的應用程式 (不在[隔離層](https://azure.microsoft.com/pricing/details/app-service/)) 會與其他應用程式共用網路基礎結構。 因此，應用程式的輸入和輸出 IP 位址可能不同，甚至會在某些情況下變更。

[App Service 環境](environment/intro.md)使用專用的網路基礎結構，因此在 App Service 環境中執行的應用程式會針對輸入和輸出連線取得靜態的專用 IP 位址。

## <a name="how-ip-addresses-work-in-app-service"></a>IP 位址在 App Service 中的運作方式

App Service 應用程式會在 App Service 方案中執行，而 App Service 方案會部署至 Azure 基礎結構中的其中一個部署單位 (內部稱為網路空間) 。 每個部署單位最多指派五個虛擬 IP 位址，其中包含一個公用輸入 IP 位址和四個輸出 IP 位址。 所有 App Service 方案都在相同的部署單位中，而在其中執行的應用程式實例則會共用同一組虛擬 IP 位址。 針對 App Service 環境 ([隔離層](https://azure.microsoft.com/pricing/details/app-service/) 中的 App Service 計畫) ，App Service 計畫是部署單位本身，因此虛擬 IP 位址是由其專用。

由於您不允許在部署單位之間移動 App Service 計畫，指派給您應用程式的虛擬 IP 位址通常會維持不變，但有例外狀況。

## <a name="when-inbound-ip-changes"></a>當輸入 IP 變更時

不論相應放大的執行個體數目為何，每個應用程式都只有一個輸入 IP 位址。 輸入 IP 位址可能會在您執行下列動作之一時變更：

- 刪除應用程式，並在不同的資源群組中重新建立， (部署單位可能會變更) 。
- 刪除資源群組 _和_ 區域組合中的最後一個應用程式，然後重新建立 (部署單位可能會變更) 。
- 刪除現有以 IP 為基礎的 TLS/SSL 系結，例如在憑證更新期間 (參閱 [更新憑證](configure-ssl-certificate.md#renew-certificate)) 。

## <a name="find-the-inbound-ip"></a>尋找輸入 IP

只需在本機終端機中執行下列命令：

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>取得靜態輸入 IP

有時候您可能會想讓應用程式使用專用的靜態 IP 位址。 若要取得靜態的輸入 IP 位址，您需要 [保護自訂網域](configure-ssl-bindings.md#secure-a-custom-domain)。 如果您實際上不需要 TLS 功能來保護您的應用程式，您甚至可以上傳此系結的自我簽署憑證。 在以 IP 為基礎的 TLS 系結中，憑證會系結至 IP 位址本身，因此 App Service 會布建靜態 IP 位址來進行。 

## <a name="when-outbound-ips-change"></a>當輸出 IP 變更時

不論相應放大的執行個體數目為何，每個應用程式在任何給定時間都會有一定數目的輸出 IP 位址。 任何來自 App Service 應用程式的輸出連線 (例如連往後端資料庫) 都會使用其中一個輸出 IP 位址來作為來源 IP 位址。 要使用的 IP 位址會在執行時間隨機選取，因此您的後端服務必須對您應用程式的所有輸出 IP 位址開啟其防火牆。

當您執行下列其中一個動作時，您的應用程式的輸出 IP 位址集會變更：

- 刪除應用程式，並在不同的資源群組中重新建立， (部署單位可能會變更) 。
- 刪除資源群組 _和_ 區域組合中的最後一個應用程式，然後重新建立 (部署單位可能會變更) 。
- 在較低層級之間調整您的應用程式 ( **基本** 、 **標準** 和 **高階) ，** 而且高階 **V2** 層 (可以將) 中的 IP 位址加入或減去。

您可以尋找應用程式可使用的所有可能輸出 IP 位址集合（不論定價層為何），方法是在 Azure 入口網站的 [屬性] 分頁中，尋找 `possibleOutboundIpAddresses` 屬性或 [ **Properties** **其他輸出 ip 位址** ] 欄位。 請參閱[尋找輸出 IP](#find-outbound-ips)。

## <a name="find-outbound-ips"></a>尋找輸出 IP

若要在 Azure 入口網站中尋找應用程式目前所使用的輸出 IP 位址，請按一下應用程式左側導覽中的 [屬性]  。 這些欄位會列在 [ **輸出 IP 位址** ] 欄位中。

您可以在 [Cloud Shell](../cloud-shell/quickstart.md) 中執行下列命令來找到同樣的資訊。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

若要尋找應用程式的 _所有_ 可能輸出 IP 位址（不論定價層為何），請按一下應用程式左側導覽中的 [ **屬性** ]。 這些欄位會列在 [ **其他輸出 IP 位址** ] 欄位中。

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
