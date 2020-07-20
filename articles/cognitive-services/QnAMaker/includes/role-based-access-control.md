---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666010"
---
提供下列角色來共同作業：

|角色|功能|API 存取|API 權限|
|--|--|--|--|
|擁有者|全部|驗證金鑰|全部|
|參與者|除了將新成員新增至角色以外的所有功能|驗證金鑰|除了將新成員新增至角色以外的所有功能|
|QnA Maker 讀取<br>閱讀文章|匯出/下載<br>測試|持有人權杖|1. 下載 KB API<br>2. 列出使用者 API 的 Kb<br>3. 取得知識庫詳細資料<br>4. 下載變更<br>產生答案 |
|QnA Maker 編輯器<br>（讀取/寫入）|匯出/下載<br>測試<br>更新 KB<br>匯出 KB<br>匯入 KB<br>取代 KB<br>建立知識庫|持有人權杖|1. 建立 KB API<br>2. 更新 KB API<br>3. 取代 KB API<br>4. 取代變更<br>5. 「訓練 API」 [在新的服務模型 v5 中]|
|認知服務使用者<br>（讀取/寫入/發行）|全部|持有人權杖|全部|