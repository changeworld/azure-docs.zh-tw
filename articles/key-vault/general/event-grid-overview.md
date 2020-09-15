---
title: 使用 Azure 事件方格監視 Key Vault
description: 使用 Azure 事件方格訂閱 Key Vault 事件
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087431"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>使用 Azure 事件方格監視 Key Vault

Key Vault 與事件方格的整合，可讓使用者在金鑰保存庫中儲存的秘密狀態變更時收到通知。 狀態變更定義為即將到期 (30 天) 、已過期的密碼，或有新版本可用之秘密的秘密。 支援全部三種祕密類型 (金鑰、憑證和祕密) 的通知。

應用程式可以使用新式無伺服器架構對這些事件做出回應，不需要複雜的程式碼或昂貴且效率不彰的輪詢服務。 透過 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)，將事件推送給事件處理常式，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的Webhook，而且只有使用時，才須支付相關費用。 如需價格的資訊，請參閱[事件格線價格](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="key-vault-events-and-schemas"></a>Key Vault 事件和結構描述

Event Grid 使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Key Vault 事件包含了回應資料變更時所需的所有資訊。 因為 eventType 屬性開頭為 "Microsoft.KeyVault"，可以藉此識別出 Key Vault 事件。

如需詳細資訊，請參閱 [Key Vault 事件結構描述](../../event-grid/event-schema-key-vault.md)。

> [!WARNING]
> 通知事件只會在新版本的秘密、金鑰和憑證上觸發，您必須先在金鑰保存庫上訂閱事件，才能接收這些通知。

## <a name="practices-for-consuming-events"></a>消費事件做法

處理 Key Vault 事件的應用程式應該遵循幾個建議做法：

* 可以將多個訂用帳戶設定為將事件路由到相同的事件處理常式。 請務必不要假設事件來自特定來源，但是要檢查訊息的主題，以確保其來自您預期的金鑰保存庫。
* 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
* 請忽略您不了解的欄位。  此做法將有助於保持未來可能新增功能的彈性。
* 使用 "subject" 前置詞和後置詞相符，將事件限制為特定的事件。

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 總覽](overview.md)
- [Azure 事件方格概觀](../../event-grid/overview.md)
- 如何： [將 Key Vault 事件路由傳送至自動化 Runbook](event-grid-tutorial.md)。
- 如何：[在金鑰保存庫祕密變更時收到電子郵件](event-grid-logicapps.md)
- [適用于 Azure Key Vault 的 Azure 事件方格事件架構](../../event-grid/event-schema-key-vault.md)
- [Azure 自動化概觀](../../automation/index.yml)
