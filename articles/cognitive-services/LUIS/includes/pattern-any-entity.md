---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591825"
---
Pattern.any 實體可讓您尋找自由格式的資料，其中實體的用字方式使其很難從語句的剩餘部分判斷實體的結尾。

人力資源應用程式可協助員工尋找公司表單。

|語句|
|--|
|**HRF-123456** 在哪裡？|
|**HRF-123234** 的作者是誰？|
|**HRF-456098** 是以法文發行的嗎？|

不過，每個表單都具備一個格式化的名稱 (上表所使用的名稱) 及一個易記名稱 (例如 `Request relocation from employee new to the company 2018 version 5`)。

含有易記表單名稱的語句看起來如下：

|語句|
|--|
|**新進員工要求調職 2018 年第 5 版**在哪裡？|
|**「新進員工要求調職 2018 年第 5 版」** 的作者是誰？|
|**新進員工要求調職 2018 年第 5 版**是以法文發行的嗎？|

這個變動長度包含可能造成 LUIS 無法正確辨識實體結束位置的字組。 在模式中使用 Pattern.any 實體可讓您指定表單名稱的開頭和結尾，讓 LUIS 能夠正確擷取表單名稱。

|範本語句的範例|
|--|
|{FormName} 在哪裡[?]|
|{FormName} 的作者是誰[?]|
|{FormName} 是以法文發行的嗎[?]|

檢閱 Pattern.any [參考](../reference-entity-pattern-any.md)資訊