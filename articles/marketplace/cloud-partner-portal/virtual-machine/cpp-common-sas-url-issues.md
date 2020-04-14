---
title: Azure 應用商店的常見 SAS URL 問題和修補程式
description: 列出使用共用存取簽章 URI 的常見問題和可能的解決方案。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273455"
---
# <a name="common-sas-url-issues-and-fixes"></a>常見 SAS URL 問題及修正

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始將 Azure 虛擬機產品的管理移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[常見 SAS URL 問題和修補程式](https://aka.ms/AzureSAS_URL_FAQ)中的說明進行操作,以管理遷移的優惠。

下表列出一些使用共用存取簽章 (用於識別及共享解決方案之上傳 VHD) 時常見問題，以及建議的解決方案。

| **問題** | **失敗訊息** | **修復** | 
| --------- | ------------------- | ------- | 
| &emsp;  *複製映像失敗* |  |  |
| 在 SAS URL 中找不到 "?" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建議工具更新 SAS URL。 |
| 不在 SAS 網址中的「st」和「se」參數 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL，將其中**開始日期**與**結束日期**修改為恰當的值。 | 
| 不在 SAS URL 中的"sp_rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | 更新 SAS Url，將權限設定為`Read`和`List`。 | 
| SAS URL 中的 VHD 名稱包含空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URL 以移除空格。 |
| SAS URL 授權錯誤 | `Failure: Copying Images. Not able to download blob due to authorization error` | 檢閱並修正 SAS URI 格式。 必要時請重新產生。 |
| SAS URL 之 "st" 和 "se" 參數沒有完整的日期時間規格 | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS 網址**開始日期**`st`與`se`**結束日期**參數( 與子字串)需要具有完整的日期`11-02-2017T00:00:00Z`時間格式,如 。 縮短版本無效。 (Azure CLI 中的某些命令可能會預設產生縮短的值)。 | 
|  |  |  |

如需詳細資訊，請參閱[使用共用存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
