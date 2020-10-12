---
title: 安裝用戶端工具
description: 安裝 azdata、kubectl、Azure CLI、psql、Azure Data Studio (測試人員) ，以及適用于 Azure Data Studio 的 Arc 延伸模組
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3eb41b868aba7e7350f54594fa43f01eeeabebc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936742"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>安裝用戶端工具，以部署和管理已啟用 Azure Arc 的資料服務

> [!IMPORTANT]
> 如果您要更新至新的每月版本，請務必同時更新至最新版本的 Azure Data Studio、Azure Data CLI (azdata) 工具和 Azure Data CLI 以及適用于 Azure Data Studio 的 Azure Arc 延伸模組。

本檔將逐步引導您完成在用戶端電腦上安裝 Azure 資料 CLI (azdata) 、Azure Data Studio、Azure CLI (az) 和 Kubernetes CLI 工具 (kubectl) 的步驟。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>用來建立和管理 Azure Arc 啟用資料服務的工具 

下表列出建立和管理 Azure Arc 啟用的資料服務所需的常用工具，以及如何安裝這些工具：

| 工具 | 必要 | 描述 | 安裝 |
|---|---|---|---|
| Azure Data CLI (azdata)  | 是 | 用於安裝和管理巨量資料叢集的命令列工具。 Azure Data CLI 也包含命令列公用程式，可使用命令 `azdata sql query` (從命令列執行單一查詢) 、 `azdata sql shell` (互動式 shell) 和，來連線至 azure SQL 和 SQL Server 實例和 Postgres 伺服器並加以查詢 `azdata postgres query` `azdata postgres shell` 。 | [安裝](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | 是 | 豐富的體驗工具，可用於連接和查詢各種不同的資料庫，包括 Azure SQL、SQL Server、PostrgreSQL 和 MySQL。 Azure Data Studio 的延伸模組可提供 Azure Arc 啟用的資料服務的系統管理體驗。 | [安裝](https://aka.ms/getazuredatastudio) |
| 適用于 Azure Data Studio 的 Azure Data CLI 擴充功能 | 是 | 如果您還沒有適用于 Azure Data Studio 的延伸模組，將會安裝 Azure 資料 CLI。| 從 Azure Data Studio 中的擴充功能庫安裝。|
| Azure Data Studio 的 Azure Arc 擴充功能 | 是 | Azure Data Studio 的擴充功能，可為 Azure Arc 啟用的資料服務提供管理體驗。 適用于 Azure Data Studio 的 Azure Data CLI 擴充功能相依。 | 從 Azure Data Studio 中的擴充功能庫安裝。|
| Azure Data Studio 中的于 postgresql 延伸模組 | 否 | 提供于 postgresql 管理功能之 Azure Data Studio 的于 postgresql 延伸模組。 | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> 從 Azure Data Studio 中的擴充功能庫安裝。|
| Azure CLI (az) <sup>1</sup> | 是 | 用於管理 Azure 服務的新式命令列介面。 搭配 AKS 部署使用，並將 Azure Arc 啟用的資料服務清查和帳單資料上傳至 Azure。  ([詳細資訊](/cli/azure/?view=azure-cli-latest&preserve-view=true)) 。 | [安裝](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl) <sup>2</sup> | 是 | 用來管理 Kubernetes 叢集的命令列工具 ([詳細資訊](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) 。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \(英文\) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) \(英文\) |
| 捲曲 <sup>3</sup> | 部分範例腳本的必要參數。 | 使用 URL 傳送資料的命令列工具。 | [Windows](https://curl.haxx.se/windows/) \(英文\) \| Linux：安裝 curl 套件 |
| Oc | 必須具備才能進行 Red Hat OpenShift 和 Azure Redhat OpenShift 部署。 |`oc` 是 Open Shift 命令列介面 (CLI)。 | [Installing the CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) (安裝 CLI)



<sup>1</sup> 您必須使用 Azure CLI 2.0.4 版版或更新版本。 如果需要，請執行 `az --version` 來尋找版本。

<sup>2</sup> 您必須使用 `kubectl` 1.13 版或更新版本。 此外，`kubectl` 的版本應該是 Kubernetes 叢集的次要版本加上或減去一。 如果您想要在 `kubectl` 用戶端上安裝特定版本，請參閱[透過 curl 安裝 `kubectl` 二進位檔](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (在 Windows 10 上，請使用 cmd.exe 執行 curl，而不是使用 Windows PowerShell)。

<sup>3</sup> 如果您使用的是 PowerShell，則捲曲是 Invoke-WebRequest Cmdlet 的別名。

## <a name="next-steps"></a>後續步驟

[建立 Azure Arc 資料控制器](create-data-controller.md)
