---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83666010"
---
提供下列角色以進行共同作業：

|角色|功能|API 存取|API 權限|
|--|--|--|--|
|擁有者|全部|驗證金鑰|全部|
|參與者|除了將新成員新增至角色之外的所有功能|驗證金鑰|除了將新成員新增至角色之外的所有功能|
|QnA Maker 讀取<br> (讀取) |匯出/下載<br>測試|持有人權杖|1. 下載 KB API<br>2. 列出使用者 API 的 Kb<br>3. 取得知識庫詳細資料<br>4. 下載變更<br>產生解答 |
|QnA Maker 編輯器<br> (讀取/寫入) |匯出/下載<br>測試<br>更新 KB<br>匯出 KB<br>匯入 KB<br>取代 KB<br>建立知識庫|持有人權杖|1. 建立 KB API<br>2. 更新 KB API<br>3. 取代 KB API<br>4. 取代變更<br>5. 「定型 API」 [在新的服務模型 v5 中]|
|認知服務使用者<br> (讀取/寫入/發佈) |全部|持有人權杖|全部|