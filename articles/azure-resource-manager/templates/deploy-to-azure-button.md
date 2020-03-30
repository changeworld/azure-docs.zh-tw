---
title: 部署至 Azure 按鈕
description: 使用按鈕從 GitHub 存儲庫部署 Azure 資源管理器範本。
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109041"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>使用部署按鈕從 GitHub 存儲庫部署範本

本文介紹如何使用 **"部署到 Azure"** 按鈕從 GitHub 存儲庫部署範本。 您可以將該按鈕直接添加到 GitHub 存儲庫中的README.md檔或引用存儲庫的網頁。

## <a name="use-common-image"></a>使用通用圖像

要將按鈕添加到網頁或存儲庫，請使用以下圖像：

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

圖像顯示為：

![部署至 Azure 按鈕](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>創建用於部署範本的 URL

要為範本創建 URL，請從回購中範本的原始 URL 開始：

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

然後，URL 對其進行編碼。 您可以使用連線編碼器或運行命令。 下面的 PowerShell 示例演示如何對值進行 URL 編碼。

```powershell
[uri]::EscapeDataString($url)
```

在 URL 編碼時，示例 URL 具有以下值。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

每個連結都以相同的基本 URL 開頭：

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

將 URL 編碼的範本連結添加到基本 URL 的末尾。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

您擁有連結的完整 URL。

## <a name="create-deploy-to-azure-button"></a>創建部署到 Azure 按鈕

最後，將連結和圖像放在一起。

要在 GitHub 存儲庫或網頁中的README.md檔中添加帶有 Markdown 的按鈕，請使用：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

對於 HTML，請使用：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>部署範本

要測試完整解決方案，請選擇以下按鈕：

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

門戶顯示一個窗格，允許您輕鬆提供參數值。 參數預先填充範本中的預設值。

![使用門戶進行部署](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>後續步驟

- 要瞭解有關範本的更多資訊，請參閱[瞭解 Azure 資源管理器範本的結構和語法](template-syntax.md)。
