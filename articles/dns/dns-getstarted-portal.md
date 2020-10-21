---
title: 快速入門：建立 DNS 區域和記錄 - Azure 入口網站
titleSuffix: Azure DNS
description: 使用此逐步快速入門指南，了解如何使用 Azure 入口網站建立 Azure DNS 區域和記錄。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: be76a7902c2747c7a8dc1bfc21d58ae88f3ff343
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282149"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Azure DNS 區域和記錄

您可以設定 Azure DNS 以解析您公用網域中的主機名稱。 例如，如果您向網域名稱註冊機構購買了 *contoso.xyz* 網域名稱，您可以設定 Azure DNS 以裝載 *contoso.xyz* 網域，並將 *`www.contoso.xyz`* 解析為您的 Web 伺服器或 Web 應用程式的 IP 位址。

在本快速入門中，您將建立一個測試網域，然後建立將 *www* 解析為 IP 位址 *10.10.10.10* 的位址記錄。

>[!IMPORTANT]
>本快速入門中的所有名稱和 IP 位址都只是範例，而不代表實際案例。

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

為了執行各個入口網站步驟，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="create-a-dns-zone"></a>建立 DNS 區域

DNS 區域包含網域的 DNS 項目。 若要開始將網域裝載到 Azure DNS 中，您應建立該網域名稱的 DNS 區域。 

**若要建立 DNS 區域：**

1. 在左上方依序選取 [建立資源]  、[網路]  和 [DNS 區域]  。

1. 在 [建立 DNS 區域]  頁面上，輸入或選取下列值：

   - **Name**：針對此快速入門的範例，輸入 *contoso.xyz*。 DNS 區域名稱可以是任何尚未在 Azure DNS 伺服器上設定的值。 實際值將是您向網域名稱註冊機構購買的網域。
   - **資源群組**：選取 [新建]  ，輸入 *MyResourceGroup*，然後選取 [確定]  。 資源群組名稱在 Azure 訂用帳戶中必須是唯一的。 

1. 選取 [建立]  。

   ![DNS 區域](./media/dns-getstarted-portal/openzone650.png)

建立區域可能需要幾分鐘的時間。

## <a name="create-a-dns-record"></a>建立 DNS 記錄

您可以為 DNS 區域內的網域建立 DNS 項目或記錄。 建立新的位址記錄或 'A' 記錄，用以將主機名稱解析為 IPv4 位址。

**若要建立 'A' 記錄：**

1. 在 Azure 入口網站中的 [所有資源]  下方，開啟 **MyResourceGroup** 資源群組中的 **contoso.xyz** DNS 區域。 您可以在 [依名稱篩選]  方塊中輸入*contoso.xyz*，以輕鬆找出該區域。

1. 在 [DNS 區域]  頁面頂端，選取 [+ 記錄集]  。

1. 在 [新增記錄集]  頁面上，輸入或選取下列值：

   - **Name**：輸入 *www*。 記錄名稱是要解析為指定 IP 位址的主機名稱。
   - **類型**：選取 [A]  。'A' 記錄是最常見的類型，但還有其他適用於郵件伺服器 ('MX')、IPv6 位址 ('AAAA') 等項目的記錄類型。 
   - **TTL**：輸入 *1*。 DNS 要求的*存留時間*會指定 DNS 伺服器和用戶端可快取回應的時間長度。
   - **TTL 單位**：選取 [小時]  。 這是 **TTL** 值的時間單位。 
   - **IP 位址**：針對此快速入門的範例，輸入 *10.10.10.10*。 此值是從記錄名稱解析出來的 IP 位址。 在實際案例中，您可以輸入 Web 伺服器的公用 IP 位址。

由於本快速入門僅供快速測試之用，因此不需要在網域名稱註冊機構設定 Azure DNS 名稱伺服器。 對於實際生產網域，您會想要讓網際網路上的所有人能夠解析主機名稱，以連線至您的 Web 伺服器或應用程式。 您應瀏覽您的網域名稱註冊機構，以將名稱伺服器記錄取代為 Azure DNS 名稱伺服器。 如需詳細資訊，請參閱[教學課程：在 Azure DNS 上託管您的網域](dns-delegate-domain-azure-dns.md#delegate-the-domain)。

## <a name="test-the-name-resolution"></a>測試名稱解析

現在您已有內含測試 'A' 記錄的測試 DNS 區域，接下來您可以使用名為 *nslookup* 的工具來測試名稱解析。 

**若要測試 DNS 名稱解析：**

1. 在 Azure 入口網站中的 [所有資源]  下方，開啟 **MyResourceGroup** 資源群組中的 **contoso.xyz** DNS 區域。 您可以在 [依名稱篩選]  方塊中輸入*contoso.xyz*，以輕鬆找出該區域。

1. 從 [概觀]  頁面上的名稱伺服器清單中複製其中一個名稱伺服器名稱。 

   ![區域](./media/dns-getstarted-portal/viewzonens500.png)

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

當您不再需要在本快速入門中建立的資源時，請刪除 **MyResourceGroup** 資源群組，以移除這些資源。 開啟 **MyResourceGroup** 資源群組，然後選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)
