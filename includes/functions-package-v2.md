---
title: 包含檔案
description: 包含檔案
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205745"
---
使用以下方法在首選開發環境中添加支援。

| 開發環境  | 應用程式類型      | 添加支援 |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# 類別庫      | [安裝 NuGet 包](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | 基於[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊擴展包](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>建議安裝[Azure 工具擴展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)。 |
| 任何其他編輯器/IDE     | 基於[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊擴展包](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure 入口網站             | 僅在門戶中連線 | 添加綁定時安裝<br /><br /> 請參閱[更新擴展](../articles/azure-functions/install-update-binding-extensions-manual.md)以更新現有綁定擴展，而無需重新發佈函數應用。 |
