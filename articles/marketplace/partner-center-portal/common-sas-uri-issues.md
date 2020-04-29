---
title: 常見的 SAS URI 問題和修正-Azure Marketplace
description: 使用共用存取簽章時所遇到的常見問題和建議的解決方法。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266236"
---
# <a name="common-sas-uri-issues-and-fixes"></a>常見的 SAS URI 問題和修正

> [!IMPORTANT]
> 我們正在將您 Azure 虛擬機器供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請遵循[常見的 SAS URI 問題和修正](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues)中的指示，Cloud Partner 入口網站來管理您的供應專案。

以下是使用共用存取簽章（用來識別及共用解決方案的已上傳 Vhd）時所遇到的常見問題，以及建議的解決方法。

| **問題** | **失敗訊息** | **補丁** |
| --------- | ------------------- | ------- |
| *複製映像失敗* |  |  |
| 在 SAS URI 中找不到 "？" | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建議的工具更新 SAS URI。 |
| "st" 和 "se" 參數不在 SAS URI 中 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 以適當的**開始日期**和**結束日期**值更新 SAS URI。 |
| "sp = rl" 不在 SAS URI 中 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI，並將許可權設定`Read`為`List`和。 |
| SAS URI 的 VHD 名稱包含空白字元 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以移除空白字元。 |
| SAS URI 授權錯誤 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 檢閱並修正 SAS URI 格式。 必要時請重新產生。 |
| SAS URI "st" 和 "se" 參數沒有完整的日期時間規格 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI**開始日期**和**結束日期**參數（`st`和`se`子字串）必須具有完整的日期時間格式，例如`11-02-2017T00:00:00Z`。 縮短的版本無效（Azure CLI 中的某些命令可能會根據預設產生縮短的值）。 |
|  |  |  |

如需詳細資訊，請參閱[使用共用存取簽章（SAS）](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。
