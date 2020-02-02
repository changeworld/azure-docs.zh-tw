---
title: 委派子域-Azure DNS
description: 在此學習路徑中，開始委派 Azure DNS 子域。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937442"
---
# <a name="delegate-an-azure-dns-subdomain"></a>委派 Azure DNS 子網域

您可以使用 Azure 入口網站委派 DNS 子網域。 例如，如果您擁有 contoso.com 網域，可以委派名為 *engineering*的子網域至可與 contoso.com 區域分開管理的其他個別區域。

您也可以依喜好使用 [Azure PowerShell](delegate-subdomain-ps.md) 委派子網域。

## <a name="prerequisites"></a>必要條件

若要委派 Azure DNS 子網域，必須先將您的公用網域委派給 Azure DNS。 如需如何設定名稱伺服器以便進行委派的指示，請參閱[將網域委派給 Azure DNS](./dns-delegate-domain-azure-dns.md)。 一旦將您的網域委派給您的 Azure DNS 區域之後，就能夠委派子網域。

> [!NOTE]
> 本文章會使用 Contoso.com 作為範例。 用您自己的網域名稱來取代 contoso.com。

## <a name="create-a-zone-for-your-subdomain"></a>建立子網域的區域

首先建立 **engineering** 子網域的區域。

1. 從 Azure 入口網站選取 [建立資源]。
2. 在 [搜尋] 方塊中，輸入 **DNS**，然後選取 [DNS 區域]。
3. 選取 [建立]。
4. 在 [建立 DNS 區域] 窗格中，在 [名稱] 文字方塊中輸入 **engineering.contoso.com**。
5. 選取您區域的資源群組。 您可能想要使用同一個資源群組作為上層區域以同時維持類似的資源。
6. 按一下頁面底部的 [新增]。
7. 部署成功後，請移至新的區域。

## <a name="note-the-name-servers"></a>記下名稱伺服器

然後，記下 engineering 子區域的四個名稱伺服器。

在 **engineering** 區域窗格中，記下區域的四個名稱伺服器。 您稍後將會用到這些名稱伺服器。

## <a name="create-a-test-record"></a>建立測試記錄

建立 **A** 記錄以供測試使用。 例如，建立一個 **www** A 記錄，並將它的 IP 位址設為 **10.10.10.10**。

## <a name="create-an-ns-record"></a>建立 NS 記錄

接下來建立 **engineering** 區域的名稱伺服器 (NS) 記錄。

1. 瀏覽至父系網域的區域。
2. 選取 [+ 記錄集]。
3. 在 [新增記錄集] 窗格中，於 [名稱] 文字方塊中輸入 **engineering**。
4. 對於 [類型]，請選取 [NS]。
5. 在 [名稱伺服器] 底下，輸入您先前從 **engineering** 區區域記下的四個名稱伺服器。
6. 按一下 [確定]。

## <a name="test-the-delegation"></a>測試委派

使用 nslookup 測試委派。

1. 開啟 PowerShell 視窗。
2. 在命令提示字元中，輸入 `nslookup www.engineering.contoso.com.`
3. 您應該會收到顯示位址 **10.10.10.10** 的非授權回答。

## <a name="next-steps"></a>後續步驟

了解如何[為 Azure 裝載的服務設定反向 DNS](dns-reverse-dns-for-azure-services.md)。