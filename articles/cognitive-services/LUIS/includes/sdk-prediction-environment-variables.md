---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772368"
---
### <a name="create-an-environment-variable"></a>建立環境變數

使用您的階段時間金鑰和執行階段端點，建立用於驗證和存取的環境變數：

* `LUIS_RUNTIME_KEY` - 用來驗證要求的執行階段資源金鑰。
* `LUIS_RUNTIME_ENDPOINT` - 與金鑰相關聯的執行階段端點。
* `LUIS_APP_ID` - 公用 LUIS IoT 應用程式識別碼為 `df67dcdb-c37d-46af-88e1-8b97951ca1c2`。
* `LUIS_APP_SLOT_NAME` - `production` 或 `staging`

如果您想要使用本快速入門來存取您自己的應用程式，您必須執行額外的步驟：
* 建立應用程式並取得應用程式識別碼
* 將執行階段金鑰指派給 LUIS 入口網站中的應用程式
* 使用瀏覽器測試 URL，確認您可以存取應用程式

請使用適合您作業系統的指示。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

新增環境變數之後，請重新啟動主控台視窗。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

新增環境變數之後，請從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

新增環境變數之後，請從主控台視窗執行 `source .bash_profile`，讓變更生效。

***
