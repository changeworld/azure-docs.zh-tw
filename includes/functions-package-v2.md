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
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027381"
---
使用下列方法，在您慣用的開發環境中新增支援。

| 開發環境  | 應用程式類型      | 若要新增支援 |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# 類別庫      | [安裝 NuGet 封裝](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | 根據[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊擴充功能組合](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>建議您安裝 [Azure 工具擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)。 |
| 任何其他編輯器/整合式開發環境     | 根據[核心工具](../articles/azure-functions/functions-run-local.md) | [註冊擴充功能組合](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure 入口網站             | 僅供線上入口網站 | 在新增繫結時安裝<br /><br /> 請參閱[更新您的擴充功能](../articles/azure-functions/functions-bindings-register.md)以更新現有的繫結擴充功能，而不需重新發行您的函式應用程式。 |