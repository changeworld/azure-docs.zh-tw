---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 引用使用 Cloud Shell 驗證的已知問題
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290087"
---
>[!NOTE]
>目前 Cloud Shell 有**已知問題**會影響這些命令群組：`az dt route`、`az dt model`、`az dt twin`。
>
>若要解決此問題，請在執行命令之前，先在 Cloud Shell 中執行 `az login`；或使用[本機 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，而不是 Cloud Shell。 如需這方面的詳細資訊，請參閱[疑難排解：Azure Digital Twins 中的已知問題](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。