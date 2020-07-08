---
title: 模型轉換
description: 描述轉換模型以進行轉譯的程式
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: db05c7a5a45221485ecb62c2c90d56be52d5ef48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808614"
---
# <a name="convert-models"></a>轉換模型

Azure 遠端呈現可讓您呈現非常複雜的模型。 若要達到最大效能，必須將資料前置處理為最佳格式。 視資料量而定，此步驟可能需要一些時間。 如果在模型載入期間花費了這段時間，這就不是很不切實際。 此外，為多個會話重複此程式會很浪費。 基於這些理由，ARR 服務會提供專用的*轉換服務*，您可以在這段時間之前執行。
轉換之後，就可以從 Azure 儲存體帳戶載入模型。

## <a name="supported-source-formats"></a>支援的來源格式

轉換服務支援下列格式：

- **FBX** （2011版和更新版本）
- **GLTF** （2.x 版）
- **GLB** （2.x 版）

關於材質屬性轉換的格式有些許差異，如[模型格式的章節材質](../../reference/material-mapping.md)對應中所列。

## <a name="the-conversion-process"></a>轉換程式

1. [準備兩個 Azure Blob 儲存體的容器](blob-storage.md)：一個用於輸入，一個用於輸出
1. 將您的模型上傳至輸入容器（選擇性地在子路徑底下）
1. 透過[模型轉換](conversion-rest-api.md)來觸發轉換程式 REST API
1. 輪詢服務的轉換進度
1. 完成後，載入模型
    - 從連結的儲存體帳戶（請參閱[建立帳戶](../create-an-account.md#link-storage-accounts)以連結儲存體帳戶中的「連結儲存體帳戶」步驟）
    - 或藉由提供*共用存取簽章（SAS）*。

所有模型資料（輸入和輸出）都會儲存在使用者提供的 Azure blob 儲存體中。 Azure 遠端呈現可讓您完全掌控資產管理。

## <a name="pricing"></a>定價

如需轉換價格的相關資訊，請參閱[遠端轉譯定價](https://azure.microsoft.com/pricing/details/remote-rendering)頁面。


## <a name="conversion-parameters"></a>轉換參數

如需各種轉換選項，請參閱[這一章](configure-model-conversion.md)。

## <a name="examples"></a>範例

- [快速入門：轉換模型以進行](../../quickstarts/convert-model.md)轉譯是如何轉換模型的逐步介紹。
- [範例 PowerShell 腳本](../../samples/powershell-example-scripts.md)示範如何使用轉換服務，可在 [*腳本*] 資料夾的 [ [ARR 範例](https://github.com/Azure/azure-remote-rendering)] 存放庫中找到。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Blob 儲存體進行模型轉換](blob-storage.md)
- [模型轉換 REST API](conversion-rest-api.md)
- [設定模型轉換](configure-model-conversion.md)
- [模型格式的材質對應](../../reference/material-mapping.md)
