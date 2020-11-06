---
title: Azure 應用程式閘道前端 IP 位址設定
description: 本文說明如何設定 Azure 應用程式閘道前端 IP 位址。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: dc5efd6ad478710ba839634a49f041211756af71
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397666"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>應用程式閘道前端 IP 位址設定

您可以將應用程式閘道設定為具有公用 IP 位址、私人 IP 位址或兩者。 當您裝載用戶端必須透過網際網路對向的虛擬 IP (VIP) 來透過網際網路存取的後端時，就需要公用 IP 位址。

## <a name="public-and-private-ip-address-support"></a>公用和私人 IP 位址支援

應用程式閘道 V2 目前不支援私人 IP 模式。 它支援下列組合：

* 私人 IP 位址和公用 IP 位址
* 僅限公用 IP 位址

如需詳細資訊，請參閱 [關於應用程式閘道](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)的常見問題。


未向網際網路公開的內部端點不需要公用 IP 位址。 這就是所謂的 *內部負載平衡器* (ILB) 端點或私人前端 IP。 應用程式閘道 ILB 適用于不會向網際網路公開的內部企業營運系統應用程式。 它也適用于在不會對網際網路公開但需要迴圈配置資源負載散發、會話或 TLS 終止的安全性界限內的多層式應用程式中的服務和層級。

僅支援一個公用 IP 位址或一個私人 IP 位址。 當您建立應用程式閘道時，請選擇前端 IP。

- 針對公用 IP 位址，您可以建立新的公用 IP 位址，或在與應用程式閘道相同的位置中使用現有的公用 IP。 如需詳細資訊，請參閱 [靜態與動態公用 IP 位址](./application-gateway-components.md#static-versus-dynamic-public-ip-address)。

- 針對私人 IP 位址，您可以從建立應用程式閘道的子網中指定私人 IP 位址。 如果您未指定，則會自動從子網選取任意 IP 位址。 您選取 (靜態或動態) 的 IP 位址類型，稍後無法變更。 如需詳細資訊，請參閱 [使用內部負載平衡器建立應用程式閘道](./application-gateway-ilb-arm.md)。

前端 IP 位址與接聽程式相關 *聯，它* 會檢查前端 ip 上的傳入要求。

## <a name="next-steps"></a>後續步驟

- [瞭解接聽程式設定](configuration-listeners.md)