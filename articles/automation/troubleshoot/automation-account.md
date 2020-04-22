---
title: 自動化帳戶疑難排解
description: 瞭解如何對 Azure 帳戶進行故障排除和解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679388"
---
# <a name="troubleshoot-the-automation-account"></a>排除自動化帳戶故障

本文討論有關使用自動化帳戶時可能會遇到的問題的解決方案。 以下各節重點介紹特定錯誤消息和每個錯誤消息的可能解決方法。 有關自動化帳戶的一般資訊,請參閱[創建 Azure 帳戶](../automation-quickstart-create-account.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>專案:無法註冊訂閱的自動化資源提供者

### <a name="issue"></a>問題

當您在自動化帳戶中使用管理解決方案時,您會遇到以下錯誤:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

未在訂閱中註冊自動化資源提供程式。

### <a name="resolution"></a>解決方案

要註冊自動化資源提供者,請在 Azure 門戶中執行以下步驟:

1. 在瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com)。

2. 導航到**訂閱**並從"訂閱"頁面選擇訂閱。   

3. 在 **「設定」** 下,選擇 **「資源提供程式**」 。。

4. 從資源提供程式清單中驗證**Microsoft.自動化**資源提供程式是否註冊。

5. 如果未列出提供者,請按[資源提供程式註冊的「解決錯誤」](/azure/azure-resource-manager/resource-manager-register-provider-errors)中所述進行註冊。

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。