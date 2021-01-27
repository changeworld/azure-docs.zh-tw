---
title: 針對 Azure 自動化帳戶問題進行疑難排解
description: 此文章說明如何針對 Azure 帳戶問題進行疑難排解及解決問題。
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896574"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>針對 Azure 自動化帳戶問題進行疑難排解

本文討論當您使用 Azure 自動化帳戶時可能會遇到問題的解決方案。 如需自動化帳戶的一般資訊，請參閱 [Azure 自動化帳戶驗證概觀](../automation-security-overview.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>案例：無法為訂用帳戶註冊自動化資源提供者

### <a name="issue"></a>問題

當您使用管理功能時，例如更新管理，在您的自動化帳戶中遇到下列錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

自動化資源提供者未在訂用帳戶中註冊。

### <a name="resolution"></a>解決方案

若要註冊自動化資源提供者，請在 Azure 入口網站中執行下列步驟：

1. 在瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com)。

2. 移至 [我的訂用帳戶]，然後選取訂用帳戶。   

3. 在 [設定] 下，選取 [資源提供者]。

4. 從資源提供者清單中，確認已註冊 **Microsoft.Automation** 資源提供者。

5. 如果未列出提供者，請依照[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)中所述進行註冊。

## <a name="next-steps"></a>後續步驟

如果本文無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 聯繫 [@AzureSupport](https://twitter.com/azuresupport)。 這是官方 Microsoft Azure 帳戶，可將 Azure 社群連結到正確的資源：解答、支援和專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
