---
title: 在 Azure 靜態 Web Apps 中設定自訂網域
description: 了解如何將自訂網域對應至 Azure 靜態 Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 61ec96a35fac6a033fe6c8b65cff156ba63e5e58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563341"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>在 Azure 靜態 Web Apps 預覽中設定自訂網域

根據預設，Azure 靜態 Web Apps 會提供自動產生的網域名稱。 本文說明如何將自訂網域名稱對應至 Azure 靜態 Web Apps 應用程式。

## <a name="prerequisites"></a>Prerequisites

- 購買的網域名稱
- 存取您網域的 DNS 組態屬性

設定網域名稱時，會使用 "A" 記錄將根網域 (例如 `example.com`) 對應至 IP 位址。 根網域必須直接對應到 IP 位址，因為 DNS 規格不允許將某個網域對應至另一個網域。

## <a name="dns-configuration-options"></a>DNS 組態選項

有幾種不同類型的 DNS 組態可供應用程式使用。

| 如果要                                 | 結果為                                                |
| -----------------------------------------------| --------------------------------------------------- |
| 支援 `www.example.com` 或 `blog.example.net`| [對應 CNAME 記錄](#map-a-cname-record)           |
| 支援 `example.com`                          | [設定根網域](#configure-a-root-domain) |
| 將所有子網域指向 `www.example.com`      | [對應萬用字元](#map-a-wildcard-domain)            |

## <a name="map-a-cname-record"></a>對應 CNAME 記錄

CNAME 記錄會將一個網域對應到另一個網域。 您可以使用 CNAME 記錄 `www.example.com` ，將、 `blog.example.com` 或任何其他子域對應至 Azure 靜態 Web Apps 所提供的自動產生網域。

1. 開啟 [Azure 入口網站](https://portal.azure.com)並使用您的 Azure 帳戶登入。

1. 搜尋並選取 [靜態 Web Apps]

1. 在 [靜態 Web Apps] 頁面上，選取您應用程式的名稱。

1. 按一下功能表中的 [自訂網域]。

1. 在 [自訂網域] 視窗中，複製 [值] 欄位中的 URL。

### <a name="configure-dns-provider"></a>設定 DNS 提供者

1. 登入網域提供者的網站。

2. 尋找管理 DNS 記錄的頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 在網站中尋找標示為 [網域名稱]、[DNS] 或 [名稱伺服器管理] 的區域。

3. 通常可透過檢視您的帳戶資訊，然後尋找 [我的網域] 之類的連結，來找到 DNS 記錄分頁。 移至該頁面，然後尋找名為**區域檔案**、**DNS 記錄**或**進階設定**之類的連結。

    下列螢幕擷取畫面是 DNS 記錄頁面的範例：

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="範例 DNS 提供者設定":::

4. 使用下列值建立新的 **CNAME** 記錄...

    | 設定             | 值                     |
    | ------------------- | ------------------------- |
    | 類型                | CNAME                     |
    | Host                | www                       |
    | 值               | 從剪貼簿貼上 |
    | TTL (如果適用) | 保留為預設值    |

5. 透過您的 DNS 提供者儲存變更。

### <a name="validate-cname"></a>驗證 CNAME

1. 返回 Azure 入口網站中的 [自訂網域] 視窗。

1. 在 [驗證自訂網域] 區段中，輸入您的網域，包括 `www` 部分。

1. 按一下 [驗證] 按鈕。

現在已設定自訂網域，DNS 提供者可能需要數小時的時間才能傳播全球變更。 您可以前往 [dnspropagation.net](https://dnspropagation.net) 來檢查傳播的狀態。 輸入您的自訂網域 (包括 `www`)，從下拉式選單中選取 CNAME，然後選取 [開始]。

如果您的 DNS 變更已填入，則網站會傳回您靜態 Web 應用程式的自動產生 URL (例如，_random-name-123456789c.azurestaticapps.net_)。

## <a name="configure-a-root-domain"></a>設定根網域

根網域是您的網域減去任何子域，包括 `www`。 例如，`www.example.com` 的根網域是 `example.com`。 這也稱為 "APEX" 網域。

雖然在預覽期間無法使用根網域支援，但您可以參閱[在 Azure 靜態 Web Apps 中設定根網域](https://burkeholland.github.io/posts/static-app-root-domain)，以深入了解如何使用靜態 Web 應用程式來設定根網域支援。

## <a name="map-a-wildcard-domain"></a>對應萬用字元網域

有時候，您希望傳送至子網域的所有流量路由至另一個網域。 常見的範例是將所有子網域流量對應到 `www.example.com`。 如此一來，即使有人輸入 `w.example.com` 而不是 `www.example.com`，要求還是會傳送至 `www.example.com`。

### <a name="configure-dns-provider"></a>設定 DNS 提供者

1. 登入網域提供者的網站。

2. 尋找管理 DNS 記錄的頁面。 每個網域提供者有自己的 DNS 記錄介面，請查閱您的提供者文件。 在網站中尋找標示為 [網域名稱]、[DNS] 或 [名稱伺服器管理] 的區域。

3. 通常可透過檢視您的帳戶資訊，然後尋找 [我的網域] 之類的連結，來找到 DNS 記錄分頁。 移至該頁面，然後尋找名為**區域檔案**、**DNS 記錄**或**進階設定**之類的連結。

    下列螢幕擷取畫面是 DNS 記錄頁面的範例：

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="範例 DNS 提供者設定":::

4. 使用下列值建立新的 **CNAME** 記錄，並將 `www.example.com` 取代為您的自訂網域名稱。

    | 設定 | 值                  |
    | ------- | ---------------------- |
    | 類型    | CNAME                  |
    | Host    | \*                     |
    | 值   | www.example.com        |
    | TTL     | 保留為預設值 |

5. 透過您的 DNS 提供者儲存變更。

現在已設定萬用字元網域，變更可能需要數小時的時間才能全球傳播。 您可以前往 [dnspropagation.net](https://dnspropagation.net) 來檢查傳播的狀態。 輸入您具有任何子網域的網域自訂網域 (並非 `www`)，從下拉式選單中選取 CNAME，然後選取 [開始]。

如果您的 DNS 變更已填入，則網站會傳回針對您靜態 Web 應用程式設定的自訂網域 (例如，`www.example.com`)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定應用程式設定](application-settings.md)
