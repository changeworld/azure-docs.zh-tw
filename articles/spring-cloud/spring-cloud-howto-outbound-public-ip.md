---
title: 如何在 Azure 春季雲端中識別輸出公用 IP 位址
description: 如何查看靜態輸出公用 IP 位址，以與外部資源（例如資料庫、儲存體、Key Vault 等）進行通訊。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663480"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>如何在 Azure 春季雲端中識別輸出公用 IP 位址

此頁面說明如何查看 Azure 春季雲端應用程式的靜態輸出公用 IP 位址。  公用 Ip 可用來與外部資源（例如資料庫、儲存體和金鑰保存庫）進行通訊。

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>IP 位址在 Azure 春季雲端中的運作方式

Azure 春季雲端服務有一或多個輸出公用 IP 位址。 輸出公用 IP 位址的數目可能會根據層級和其他因素而有所不同。 

輸出公用 IP 位址通常是固定的，而且保持不變，但有例外狀況。

## <a name="when-outbound-ips-change"></a>當輸出 IP 變更時

每個 Azure 春季雲端實例在任何指定時間都有設定的輸出公用 IP 位址數目。 任何來自應用程式的輸出連線（例如連至後端資料庫）都會使用其中一個輸出公用 IP 位址作為來源 IP 位址。 系統會在執行時間隨機選取 IP 位址，因此您的後端服務必須對所有輸出 IP 位址開啟其防火牆。

當您執行下列其中一個動作時，輸出公用 Ip 的數目會變更：

- 在各層之間升級 Azure 春季 Cloud 實例。
- 針對商務需求提出更多輸出公用 Ip 的支援票證。

## <a name="find-outbound-ips"></a>尋找輸出 IP

若要在 Azure 入口網站中尋找您的服務實例目前使用的輸出公用 IP 位址，請在實例的左側導覽窗格中，按一下 [ **網路** ]。 這些欄位會列在 [ **輸出 IP 位址** ] 欄位中。

您可以在 Cloud Shell 中執行下列命令，以尋找相同的資訊。

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
* [深入了解 Azure 資源受控識別](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [深入瞭解 Azure 中的金鑰保存庫春季雲端](spring-cloud-tutorial-managed-identities-key-vault.md)
