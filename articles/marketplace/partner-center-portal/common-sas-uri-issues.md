---
title: 常見的 SAS URI 問題和修正程式 - Azure Marketplace
description: 使用共用存取簽章時遇到的常見問題和建議的解決方法。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724593"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常見 SAS URL 問題和修正程式

以下為使用共用存取簽章 (用於識別及共享解決方案之上傳 VHD) 時常見問題，以及建議的解決方案。

| **問題** | 失敗訊息 | 修正 |
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
