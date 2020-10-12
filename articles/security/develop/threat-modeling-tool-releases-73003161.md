---
title: Microsoft Threat Modeling Tool 版本 03/22/2020-Azure
description: 記載威脅模型化工具 release 7.3.00316.1 的版本資訊。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 39963e9dadca0ee0be7bb8ad0ebf824c287bfee0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317900"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新版本 7.3.00316.1-03/22/2020

Microsoft Threat Modeling Tool (TMT) 的版本7.3.00316.1 已于 22 2020 年3月發行，並包含下列變更：

- 協助工具改進
- 錯誤修正
- 新增 DiagramReader 功能

## <a name="notable-bug-fixes"></a>值得注意的錯誤修正

### <a name="exporting-the-threat-list-to-csv"></a>將威脅清單匯出至 CSV

匯出至 CSV 函式不一致地選取要匯出威脅清單中的哪些欄位。 現在會將威脅清單中的所有欄位匯出至 CSV 檔案。 

### <a name="ux-bugs"></a>UX bug

- 主要工作流程中的 [說明] 功能表 (建立/開啟/分析) ，而範本編輯器體驗現在具有一致的功能表選項。
- [樣板] 窗格中的 [搜尋] 列現在已新增標準資料指標和適當的標籤。

## <a name="new-features"></a>新功能

### <a name="diagramreader-feature-has-been-added"></a>已新增 DiagramReader 功能

當模型開啟時，主功能表中已新增 DiagramReader 功能。 這項功能會將模型的圖形表示轉換成以文字為基礎的敘述。 

## <a name="system-requirements"></a>系統需求

- 支援的作業系統：
  - [Microsoft Windows 10 年度更新版](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 或更新版本
- 需要 .NET 版本：
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更新版本
- 其他需求：
  - 網際網路連線，可接收工具和範本的更新

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
