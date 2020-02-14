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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205745"
---
使用下列方法，在您慣用的開發環境中新增支援。

| 開發環境  | 應用程式類型      | 新增支援 |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# 類別庫      | [安裝 NuGet 套件](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | 根據[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊延伸模組配套](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>建議您安裝[Azure Tools 擴充](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)功能。 |
| 任何其他編輯器/IDE     | 根據[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊延伸模組配套](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure 入口網站             | 僅在入口網站中上線 | 在新增系結時安裝<br /><br /> 請參閱[更新您的擴充](../articles/azure-functions/install-update-binding-extensions-manual.md)功能以更新現有的系結延伸模組，而不需要重新發佈函式應用程式。 |
