---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173841"
---
每個使用點對站連線來連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 您會從根憑證產生用戶端憑證，並將其安裝在每部用戶端電腦上。 如果您沒有安裝有效的用戶端憑證，用戶端嘗試連線至 VNet 時所進行的驗證將會失敗。

您可以為每個用戶端產生唯一的憑證，也可以對多個用戶端使用相同的憑證。 產生唯一用戶端憑證的優點是能夠撤銷單一憑證。 否則，如果多個用戶端使用相同的用戶端憑證進行驗證，而您要撤銷它時，就必須為每個使用該憑證的用戶端產生並安裝新的憑證。

您可以使用下列方法來產生用戶端憑證︰

- **企業證書：**

  - 如果使用企業證書解決方案，則生成具有通用名稱值格式*名稱\@的*用戶端憑證yourdomain.com 。 請使用此格式，而不是「網域名稱\使用者名稱」** 的格式。
  - 請確定用戶端憑證所根據的憑證範本，是將「用戶端驗證」** 列為使用者清單中第一個項目的使用者憑證範本。 按兩下憑證，然後檢視 [詳細資料]**** 索引標籤中的 [增強金鑰使用方法]****，即可檢查憑證。

- **自簽名根憑證：** 按照以下 P2S 證書文章中的步驟操作，以便您創建的用戶端憑證與 P2S 連接相容。 這些文章中的步驟都會產生相容的用戶端憑證： 

  * [Windows 10 PowerShell 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert)：這些指示需要 Windows 10 和 PowerShell，以產生憑證。 產生的憑證可以安裝在任何支援的 P2S 用戶端。
  * [製作證書說明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：如果您無法訪問 Windows 10 電腦以生成證書，請使用 MakeCert。 雖然 MakeCert 已被取代，但您仍可用它來產生憑證。 您可以將產生的憑證安裝在任何支援的 P2S 用戶端。
  * [Linux 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  當您從自我簽署根憑證產生用戶端憑證時，此憑證會自動安裝在您用來產生它的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，請將其匯出為 .pfx 檔案 (包含整個憑證鏈結)。 這麼做將會建立一個 .pfx 檔案，其中包含用戶端進行驗證所需的根憑證資訊。 

**匯出憑證**

如需匯出憑證的步驟，請參閱[使用 PowerShell 來產生並匯出點對站的憑證](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)。
