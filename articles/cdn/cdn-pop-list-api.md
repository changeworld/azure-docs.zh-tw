---
title: 檢索 Azure CDN 的 POP IP 清單*微軟文件
description: 瞭解如何檢索當前 POP 清單。
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
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a58fd7c6f50cd46ac3c34cd7e5bd329c0007e5f6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260185"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>檢索 Azure CDN 的 POP IP 清單

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>檢索 Azure CDN 的目前 Verizon POP IP 清單

您可以使用 REST API 來為 Verizon 的存在點 (PoP) 伺服器擷取一組 IP。 這些 POP 伺服器會將要求發給與 Verizon 設定檔 (**來自 Verizon 的 Azure CDN 標準**或**來自 Verizon 的 Azure CDN 進階**) 上的 Azure 內容傳遞網路 (CDN) 端點相關聯的原始伺服器。 請注意，這組 IP 與用戶端在將要求發給 POP 時所會看到的 IP 不同。 

如需用於擷取 POP 清單的 REST API 作業語法，請參閱[邊緣節點 - 清單](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)。

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>檢索 Azure CDN 的目前 Microsoft POP IP 清單

要鎖定應用程式以僅接受來自 Microsoft 的 Azure CDN 的流量,您需要為後端設置 IP ACL。 您還可以限制 Azure CDN 從 Microsoft 發送的標頭「X-前轉主機」的接受值集。 這些步驟如下:

為後端介面設定 IP 交流,以便僅接受來自 Microsoft 後端 IP 位址空間和 Azure 基礎結構服務的 Azure CDN 的流量。 

* 來自微軟 IPv4 後端 IP 空間的 Azure CDN:147.243.0.0/16
* 來自微軟 IPv6 後端 IP 空間的 Azure CDN: 2a01:111:2050::/44

適用於 Microsoft 服務的 IP 範圍和服務標記[可在此處](https://www.microsoft.com/download/details.aspx?id=56519)找到


## <a name="typical-use-case"></a>典型的使用案例

為確保安全，您可以使用此 IP 清單，強制規定只有有效的 Verizon POP 才能將要求發給原始伺服器。 例如，如果有使用者探索到 CDN 端點原始伺服器的主機名稱或 IP 位址，該使用者就可以直接將要求發給原始伺服器，因而繞過 Azure CDN 所提供的調整和安全性功能。 只要將所傳回清單中的 IP 設定為原始伺服器上唯一允許的 IP，就能避免此情形。 為了確保您擁有最新的 POP 清單，請至少每天擷取一次該清單。 

## <a name="next-steps"></a>後續步驟

如需 REST API 的相關資訊，請參閱 [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)。
