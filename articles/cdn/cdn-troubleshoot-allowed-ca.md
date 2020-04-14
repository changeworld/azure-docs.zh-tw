---
title: 允許 CA 在 Azure CDN 上啟用自訂 HTTPS
description: 如果您使用自己的憑證來啟用自訂網域上的 HTTPS，就必須使用允許的憑證授權單位 (CA) 來建立它。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259965"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位

使用[自己的](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)憑證訪問 Azure 內容傳遞網路 (CDN) 自訂網域時,必須滿足特定的證書要求。 Microsoft 設定檔**中的 Azure CDN 標準**要求以下清單中已批准的證書頒發機構 (CA) 之一的證書。 如果來自未經批准的 CA 的證書或使用自簽名證書,則請求將被拒絕。 **來自 Verizon 的 Azure CDN 標準版**和來自 Verizon 設定檔**的 Azure CDN 進階版**接受來自任何有效 CA 的任何有效憑證。

> [!NOTE]
> 使用您自己的憑證啟用自定義網功能的選項*不適用於*Akamai 設定檔中的**Azure CDN 標準**版。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

