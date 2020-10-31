---
title: Azure 串流分析的持續整合和部署
description: 本文概述 Azure 串流分析的持續整合和部署 (CI/CD) 管線。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: b5057eb8c84e839f504060228986ea759c8bdc3d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123162"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>適用于 Azure 串流分析的持續整合和部署 (CI/CD) 

您可以使用原始檔控制整合，持續部署 Azure 串流分析作業。 原始檔控制整合可讓程式碼更新的工作流程觸發資源部署到 Azure。 本文概述建立持續整合和部署 (CI/CD) 管線的基本步驟。

如果您不熟悉 Azure 串流分析，請開始使用 [Azure 串流分析快速入門](stream-analytics-quick-create-portal.md)。

## <a name="create-a-cicd-pipeline"></a>建立 CI/CD 管線

遵循本指南中的步驟，建立適用于串流分析的 CI/CD 管線。

1. 開發 Azure 串流分析查詢。

   使用適用于 [Visual Studio Code](./quick-create-visual-studio-code.md) 或 [Visual Studio](stream-analytics-quick-create-vs.md) 的 Azure 串流分析工具，在 [本機開發及測試查詢](develop-locally.md)。 您也可以將 [現有的作業匯出](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) 到本機專案。

2. 將您的 Azure 串流分析專案認可到原始檔控制系統，例如 Git 儲存機制。

3. 使用 [Azure 串流分析 CI/CD 工具](cicd-tools.md) 來建立專案，並產生部署的 Azure 資源管理範本。

4. 針對品質回歸執行 [自動化腳本測試](cicd-tools.md#automated-test) 。

5. 自動將[作業部署](cicd-tools.md#deploy-to-azure)到 Azure。

## <a name="auto-build-test-and-deploy"></a>自動建立、測試及部署

您可以使用命令列和 [Azure 串流分析 CI/CD 工具](cicd-tools.md) 來自動建立、測試及部署。 您也可以在 [Azure Pipelines](set-up-cicd-pipeline.md)中設定 CI/CD 管線。 Azure Pipelines，以啟用更先進的功能，例如管線管理、視覺效果和觸發程式。

## <a name="next-steps"></a>後續步驟

* [使用 CI/CD 工具將 Azure 串流分析作業的組建、測試和部署自動化](cicd-tools.md)
* [使用 Azure Pipelines 為串流分析作業設定 CI/CD 管線](set-up-cicd-pipeline.md)