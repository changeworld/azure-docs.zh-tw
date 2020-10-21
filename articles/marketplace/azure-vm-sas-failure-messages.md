---
title: 虛擬機器 SAS 失敗訊息-Azure Marketplace
description: 使用共用存取簽章 (SAS) 時遇到的常見問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 7b91cf3d49d88501fb908d157892ec3eb24bee6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283618"
---
# <a name="virtual-machine-sas-failure-messages"></a>虛擬機器 SAS 失敗訊息

以下為使用共用存取簽章 (用於識別及共享解決方案之上傳 VHD) 時常見問題，以及建議的解決方案。

| 問題 | 失敗訊息 | 修正 |
| --------- | ------------------- | ------- |
| 複製映像失敗 |  |  |
| 在 SAS URI 中找不到 "?" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建議工具更新 SAS URI。 |
| "st" 和 "se" 參數不在 SAS URI 中 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI，將**開始日期**與**結束日期**修改為恰當的值。 |
| "sp=rl" 不在 SAS URI 中 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URl，將權限設定為 `Read` 和 `List`。 |
| SAS URI 中的 VHD 名稱包含空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以移除空格。 |
| SAS URI 授權錯誤 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 檢閱並修正 SAS URI 格式。 必要時請重新產生。 |
| SAS URI 之 "st" 和 "se" 參數沒有完整的日期時間規格 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI **開始日期**與**結束日期**參數 (`st` 和 `se` 子字串) 都需要完整的日期時間格式，例如：`11-02-2017T00:00:00Z`。 縮短的版本無效 (Azure CLI 中的某些命令可能預設會產生縮短的值)。 |
|  |  |  |

如需詳細資料，請參閱[使用共用存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

## <a name="next-steps"></a>後續步驟

- [產生 SAS URI](azure-vm-get-sas-uri.md)
