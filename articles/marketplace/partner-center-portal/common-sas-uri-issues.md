---
title: 常見的 SAS URI 問題和修復 - Azure 應用商店
description: 使用共享訪問簽名時遇到的常見問題和建議的解決方案。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266236"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常見的 SAS URI 問題和修復

> [!IMPORTANT]
> 我們將 Azure 虛擬機器產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照[「常見 SAS URI 問題和](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues)雲合作夥伴門戶修補程式」中的說明進行操作,以管理您的優惠。

以下是使用共享訪問簽名(用於標識和共用已上傳的 VHD 用於解決方案)時遇到的常見問題,以及建議的解決方案。

| **問題** | **失敗訊息** | **修復** |
| --------- | ------------------- | ------- |
| *複製映像失敗* |  |  |
| "?" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用推薦的工具更新 SAS URI。 |
| 不在 SAS URI 中的「st」和「se」參數 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用正確的**開始日期**和**結束日期**值更新 SAS URI。 |
| 不在 SAS URI 中的"sp_rl" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 將權限設定`Read`為`List`和更新 SAS URI。 |
| SAS URI 具有 VHD 名稱中的空白 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以刪除空格。 |
| SAS URI 授權錯誤 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 檢閱並修正 SAS URI 格式。 必要時請重新產生。 |
| SAS URI"st"和"se"參數沒有完整的日期時間規範 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI**開始日期**與`st`**結束日期**參數(`se`與子字串)必須具有完整的日期時間`11-02-2017T00:00:00Z`格式,如 。 縮短的版本無效(默認情況下,Azure CLI 中的某些命令可能會生成縮短的值)。 |
|  |  |  |

有關詳細資訊,請參閱[使用共享訪問簽名 (SAS)。](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
