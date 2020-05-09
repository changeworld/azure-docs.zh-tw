---
title: Azure 自動化帳戶疑難排解
description: 瞭解如何針對 Azure 帳戶進行疑難排解及解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864125"
---
# <a name="troubleshoot-an-azure-automation-account"></a>針對 Azure 自動化帳戶進行疑難排解

本文討論當您使用 Azure 自動化帳戶時可能會遇到之問題的解決方案。 如需自動化帳戶的一般資訊，請參閱[建立 Azure 帳戶](../automation-quickstart-create-account.md)。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>案例：無法註冊訂用帳戶的自動化資源提供者

### <a name="issue"></a>問題

當您使用自動化帳戶中的管理解決方案時，您會遇到下列錯誤：

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

自動化資源提供者未在訂用帳戶中註冊。

### <a name="resolution"></a>解決方案

若要註冊自動化資源提供者，請遵循 Azure 入口網站中的下列步驟：

1. 在瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com)。

2. 移至 [訂用帳戶 **]，然後選取您的訂**用帳戶。   

3. 在 [**設定**] 下，選取 [**資源提供者**]。

4. 從資源提供者清單中，確認已註冊**Microsoft Automation**資源提供者。

5. 如果未列出提供者，請依照[解決資源提供者註冊的錯誤](/azure/azure-resource-manager/resource-manager-register-provider-errors)中所述進行註冊。

## <a name="next-steps"></a>後續步驟

如果本文無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 連接[@AzureSupport](https://twitter.com/azuresupport)。 這是將 Azure 社區連接到正確資源的官方 Microsoft Azure 帳戶：解答、支援和專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。