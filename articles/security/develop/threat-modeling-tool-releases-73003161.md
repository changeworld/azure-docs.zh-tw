---
title: 微軟威脅建模工具版本 03/22/2020 - Azure
description: 記載 Threat Modeling Tool 的版本資訊
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146858"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>威脅建模工具更新版本 7.3.00316.1 - 03/22/2020

Microsoft 威脅建模工具 （TMT） 的版本 7.3.00316.1 于 2020 年 3 月 22 日發佈，其中包含以下更改：

- 協助工具增強功能
- 錯誤修正
- 新的圖表閱讀器功能

## <a name="notable-bug-fixes"></a>值得注意的錯誤修復

### <a name="exporting-the-threat-list-to-csv"></a>將威脅清單匯出到 CSV

匯出到 CSV 函數不一致地選擇將從威脅清單中匯出哪些欄位。 現在，威脅清單中的所有欄位都將匯出到 CSV 檔中。 

### <a name="ux-bugs"></a>UX 錯誤

- 主工作流中的説明功能表（創建/打開/分析）和範本編輯器體驗現在具有一致的功能表選項。
- 模具窗格中的搜索欄現在具有標準游標，並添加了相應的標籤。

## <a name="new-features"></a>新功能

### <a name="diagramreader-feature-has-been-added"></a>已添加圖表閱讀器功能

在模型打開時，主功能表中添加了新的圖表閱讀器功能。 此功能將模型的圖形表示形式轉換為基於文本的敘述。 

## <a name="system-requirements"></a>系統需求

- 支援的作業系統：
  - [微軟視窗 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- .NET 版本要求：
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 其他需求：
  - 用於接收工具更新和範本的互聯網連接

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
