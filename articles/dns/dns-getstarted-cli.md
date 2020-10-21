---
title: 快速入門：建立 Azure DNS 區域和記錄 - Azure CLI
titleSuffix: Azure DNS
description: 快速入門 - 了解如何在 Azure DNS 中建立 DNS 區域和記錄。 這份逐步指南將引導您使用 Azure CLI 建立和管理第一個 DNS 區域和記錄。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d02095bbe3326209cff6b4e99858b2598c7a914
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282228"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure DNS 區域和記錄

本文將逐步引導您使用 Azure CLI (適用於 Windows、Mac 和 Linux)，建立第一個 DNS 區域和記錄。 您也可以使用 [Azure 入口網站](dns-getstarted-portal.md)或 [Azure PowerShell](dns-getstarted-powershell.md) 來執行這些步驟。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 最後，若要將 DNS 區域發佈至網際網路，您需要設定網域的名稱伺服器。 以下說明上述各步驟。

Azure DNS 也支援私人 DNS 區域。 若要深入了解私人 DNS 區域，請參閱[使用 Azure DNS 私人網域](private-dns-overview.md)。 如需如何建立私人 DNS 區域的範例，請參閱[利用 CLI 開始使用 Azure DNS 私人區域](./private-dns-getstarted-cli.md)。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure CLI 2.0.4 版或更新版本](/cli/azure/install-azure-cli) (如果您在本機執行 Azure CLI)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，請先建立包含 DNS 區域的資源群組：

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `az network dns zone create` 命令建立 DNS 區域。 若要查看此命令的說明，請輸入 `az network dns zone create -h`。

下列範例會在 MyResourceGroup  資源群組中建立稱為 contoso.xyz  的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>建立 DNS 記錄

若要建立 DNS 記錄，請使用 `az network dns record-set [record type] add-record` 命令。 如需 A 記錄的說明，請參閱 `azure network dns record-set A add-record -h`。

下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.xyz" 中，建立具有相對名稱 "www" 的記錄。 記錄集的完整名稱是 "www.contoso.xyz"。 記錄類型為 "A"，IP 位址是 "10.10.10.10"，預設 TTL 為 3600 秒 (1 小時)。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請執行︰

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>測試名稱解析

現在您已有內含測試 'A' 記錄的測試 DNS 區域，接下來您可以使用名為 *nslookup* 的工具來測試名稱解析。 

**若要測試 DNS 名稱解析：**

1. 執行下列 Cmdlet 來取得您區域中的名稱伺服器清單：

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. 從上一個步驟的輸出複製其中一個名稱伺服器名稱。

1. 開啟命令提示字元，並執行下列命令：

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例如：

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   您應該會看到類似於下列畫面的內容：

   ![螢幕擷取畫面顯示命令提示字元視窗，其中包含 n s 查閱命令，以及伺服器、位址、名稱和位址的值。](media/dns-getstarted-portal/nslookup.PNG)

主機名稱 **www\.contoso.xyz** 解析為 **10.10.10.10**，正如您所設定。 此結果確認了名稱解析正常運作。

## <a name="clean-up-resources"></a>清除資源

不再需要時，您可以藉由刪除資源群組，刪除在本快速入門中建立的所有資源：

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

既然您已經使用 Azure CLI 建立第一個 DNS 區域和第一筆記錄，就可以在自訂網域中為 Web 應用程式建立記錄。

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)
