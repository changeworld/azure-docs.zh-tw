---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173839"
---
使用透過企業解決方案產生的根憑證 (建議)，或產生自我簽署憑證。 建立根憑證之後，將公開憑證資料 (不是私密金鑰) 匯出為 Base64 編碼的 X.509 .cer 檔案。 然後，將公開憑證資料上傳至 Azure 伺服器。

* **企業證書：** 如果您使用的是企業解決方案，則可以使用現有的憑證連結。 取得您想要使用的根憑證 .cer 檔案。
* **自簽名根憑證：** 如果不使用企業證書解決方案，請創建自簽名根憑證。 否則，您所建立的憑證將無法與 P2S 連線相容，而且用戶端會在嘗試連線時收到連線錯誤訊息。 您可以使用 Azure PowerShell、MakeCert 或 OpenSSL。 下列文章中的步驟將說明如何產生相容的自我簽署根憑證：

  * [Windows 10 PowerShell 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)：這些指示需要 Windows 10 和 PowerShell，以產生憑證。 從根憑證產生的用戶端憑證可以安裝於任何支援的 P2S 用戶端上。
  * [MakeCert 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：如果您無法存取 Windows 10 電腦來產生憑證，則可使用 MakeCert。 雖然 MakeCert 已被取代，但您仍可用它來產生憑證。 從根憑證產生的用戶端憑證可以安裝於任何支援的 P2S 用戶端上。
  * [Linux 指示](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
