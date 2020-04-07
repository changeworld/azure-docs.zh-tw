---
title: 模型轉換
description: 描述轉換模型進行成像的過程
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681489"
---
# <a name="convert-models"></a>轉換模型

Azure 遠端呈現允許您呈現非常複雜的模型。 為了實現最大的性能,必須預處理數據才能以最佳格式進行處理。 根據數據量,此步驟可能需要一段時間。 如果此時間在模型載入期間花費,則不切實際。 此外,在多個會話中重複此過程也是浪費。 由於這些原因,ARR 服務提供專用*的轉換服務*,您可以提前運行。
轉換後,可以從 Azure 儲存帳戶載入模型。

## <a name="supported-source-formats"></a>支援的來源格式

轉換服務支援以下格式:

- **FBX** (2011 年版及以上)
- **GLTF(** 版本 2.x)
- **GLB(** 版本 2.x)

格式在材料屬性轉換方面存在細微差異,如[模型格式的章節材料映射](../../reference/material-mapping.md)中列出的。

## <a name="the-conversion-process"></a>轉換程序

1. [準備兩個 Azure Blob 儲存容器](blob-storage.md):一個用於輸入,一個用於輸出
1. 將模型上傳到輸入容器(選擇在子路徑下)
1. 以[模型轉換 REST API](conversion-rest-api.md)觸發轉換程序
1. 輪詢服務以進行轉換進度
1. 完成後,載入模型
    - 從連結的儲存帳戶(請參閱[創建帳戶](../create-an-account.md#link-storage-accounts)以連結儲存帳戶的「連結存儲帳戶」步驟)
    - 或透過提供*分享存取簽章 (SAS)*。

所有模型數據(輸入和輸出)都存儲在使用者提供的 Azure Blob 儲存中。 Azure 遠端呈現使您能夠完全控制資產管理。

## <a name="conversion-parameters"></a>轉換參數

有關各種轉換選項,請參閱[本章](configure-model-conversion.md)。

## <a name="examples"></a>範例

- [快速入門:轉換渲染模型](../../quickstarts/convert-model.md)是如何轉換模型的分步介紹。
- [範例 PowerShell 文稿](../../samples/powershell-example-scripts.md)(展示轉換服務的使用方式)可以在*Scripts*資料夾中的[ARR 範例儲存庫](https://github.com/Azure/azure-remote-rendering)中找到。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Blob 儲存的模型轉換](blob-storage.md)
- [模型轉換 REST API](conversion-rest-api.md)
- [設定模型轉換](configure-model-conversion.md)
- [模型格式的材料對應](../../reference/material-mapping.md)
