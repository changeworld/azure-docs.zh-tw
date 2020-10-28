---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 引用使用 Cloud Shell 驗證的已知問題
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325056"
---
>[!NOTE]
>從 *https://shell.azure.com* 執行時，Cloud Shell 中目前有 **已知問題** 會影響這些命令群組：`az dt route` 、`az dt model` 、`az dt twin` 。
>
>若要解決此問題，您可以執行下列任何一項動作：
> * 執行命令之前，先在 Cloud Shell 中執行 `az login`。
> * 在 Azure 入口網站中開啟 [Cloud Shell] 窗格，並從該處完成您的 Cloud Shell 工作。
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="已醒目提示 [Cloud Shell] 圖示的 Azure 入口網站檢視，而且 Cloud Shell 顯示在入口網站視窗底部":::
> * 使用[本機 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，而不是 Cloud Shell。
>
>如需此問題的詳細資訊，請參閱 [*疑難排解：* Azure Digital Twins 中的已知問題](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。