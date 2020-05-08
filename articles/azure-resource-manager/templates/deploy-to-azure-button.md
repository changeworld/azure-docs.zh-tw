---
title: 部署至 Azure 按鈕
description: 使用按鈕，從 GitHub 存放庫部署 Azure Resource Manager 範本。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: e403d095f7419ed13742dcfa8e8407a92ad10be0
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744100"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>使用 [部署] 按鈕來部署 GitHub 存放庫中的範本

本文說明如何使用 [**部署至 Azure** ] 按鈕，從 GitHub 存放庫部署範本。 您可以將按鈕直接新增至 GitHub 存放庫中的 README.md 檔案，或加入至參考存放庫的網頁。 這個方法只支援資源群組層級部署。

## <a name="use-common-image"></a>使用通用映射

若要將按鈕新增至您的網頁或存放庫，請使用下列影像：

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

影像會顯示為：

![部署至 Azure 按鈕](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>建立用來部署範本的 URL

若要建立範本的 URL，請從存放庫中範本的原始 URL 開始：

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

然後，URL 將它編碼。 您可以使用線上編碼器或執行命令。 下列 PowerShell 範例示範如何對值進行 URL 編碼。

```powershell
[uri]::EscapeDataString($url)
```

URL 編碼時，範例 URL 具有下列值。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

每個連結的開頭都是相同的基底 URL：

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

將 URL 編碼的範本連結新增至基底 URL 的結尾。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

您有連結的完整 URL。

## <a name="create-deploy-to-azure-button"></a>建立 [部署至 Azure] 按鈕

最後，將連結和影像放在一起。

若要在 GitHub 存放庫或網頁的 README.md 檔案中新增具有 Markdown 的按鈕，請使用：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

若是 HTML，請使用：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>部署範本

若要測試完整的解決方案，請選取下列按鈕：

[![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

入口網站會顯示一個窗格，讓您輕鬆地提供參數值。 這些參數會預先填入範本中的預設值。

![使用入口網站進行部署](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解範本，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
