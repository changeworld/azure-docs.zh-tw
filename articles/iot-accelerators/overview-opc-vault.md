---
title: 什麼是 OPC 保存庫 - Azure | Microsoft Docs
description: 此文章提供 OPC 保存庫的概觀。 它可以在雲端中設定、註冊和管理 OPC UA 應用程式的憑證生命週期。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826208"
---
# <a name="what-is-opc-vault"></a>什麼是 OPC 保存庫？

OPC 保存庫是一項微服務，可以設定、註冊和管理雲端中的 OPC UA 伺服器和用戶端應用程式的憑證生命週期。 本文將說明 OPC 保存庫的簡單使用案例。

## <a name="certificate-management"></a>憑證管理

例如，某家製造公司需要其 OPC UA 伺服器電腦連線至其新建置的用戶端應用程式。 當製造商初次存取伺服器電腦時，OPC UA 伺服器應用程式會立即顯示錯誤訊息，以指出用戶端應用程式並不安全。 這是 OPC UA 伺服器電腦的內建機制，旨在避免任何未經授權的應用程式存取，這可以防止工廠遭到惡毒駭客攻擊。

## <a name="application-security-management"></a>應用程式安全性管理
安全性專業人員可使用 OPC 保存庫微服務輕鬆地讓 OPC UA 伺服器與任何用戶端應用程式進行通訊，因為 OPC 保存庫具有憑證登錄、儲存體和生命週期管理的所有功能。 OPC UA 伺服器現在已安全地連線，可與新建置的用戶端應用程式進行通訊

## <a name="the-complete-opc-vault-architecture"></a>完整的 OPC 保存庫架構
下圖顯示完整的 OPC 保存庫架構。

![OPC 保存庫架構](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>後續步驟

現在您已了解 OPC 保存庫及其使用方式，以下是建議執行的下一個步驟：

> [!div class="nextstepaction"]
> [OPC 保存庫架構](overview-opc-vault-architecture.md)
