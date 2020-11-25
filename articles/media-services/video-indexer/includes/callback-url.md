---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994483"
---
用來通知客戶下列事件的 URL (使用 POST 要求)：

- 索引狀態變更： 
    - 內容：    
    
        |名稱|描述|
        |---|---|
        |id|影片識別碼|
        |state|影片狀態|  
    - 範例： HTTPs： \/ /test.com/notifyme?projectName=MyProject&識別碼 = 1234abcd&狀態 = 已處理
- 在影片中識別到的人員：
  - 屬性
    
      |名稱|描述|
      |---|---|
      |id| 影片識別碼|
      |faceId|影片索引中出現的臉部識別碼|
      |knownPersonId|臉部模型中唯一的個人識別碼|
      |personName|人員名稱|
        
    - 範例： HTTPs： \/ /test.com/notifyme?projectName=MyProject&id = 1234abcd&faceid = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&personName = Inigo_Montoya 
