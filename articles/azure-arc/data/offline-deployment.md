---
title: 離線部署
description: 離線部署可讓您從私人容器登錄提取容器映射，而不是從 Microsoft Container Registry 提取。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 575903654a165bef0d09ac6abf0793af3f6784e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936711"
---
# <a name="offline-deployment-overview"></a>離線部署總覽

通常，在建立 Azure Arc 資料控制器、SQL 受控實例和于 postgresql 超大規模伺服器群組時所使用的容器映射，會直接從 Microsoft Container Registry 提取 (MCR) 。 在某些情況下，您要部署到其中的環境將無法連線到 Microsoft Container Registry。  在 _這種情況_ 下，您可以使用可存取 Microsoft container registry 的電腦來提取容器映射，然後標記並推送至私人容器登錄 _，此登錄可從_ 您想要部署 Azure Arc 啟用的資料服務的環境中連接。

由於針對 Azure Arc 啟用的資料服務提供每月更新，且有大量的容器映射，因此最好使用腳本來執行此程式，以提取、標記容器映射，並將其推送至私人容器登錄。  腳本可以是自動化或手動執行。

您可以在 Azure Arc GitHub 存放庫中找到 [範例腳本](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) 。

> [!NOTE]
> 此腳本需要安裝 python 和 [DOCKER CLI](https://docs.docker.com/install/)。

腳本會以互動方式提示您輸入下列資訊。  或者，如果您想要在沒有互動式提示的情況下執行腳本，您可以在執行腳本之前設定對應的環境變數。

|Prompt|環境變數|注意|
|---|---|---|
|提供來源容器登錄-按 ENTER 以使用 `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|一般而言，您會從 Microsoft Container Registry 提取映射，但如果您使用不同的登錄來參與私人預覽，則可以使用提供給您的資訊作為預覽程式的一部分。|
|提供來源容器登錄存放庫-按 ENTER 以使用 `arcdata` ：|SOURCE_DOCKER_REPOSITORY|如果您要從 Microsoft Container Registry 提取，存放庫將會是 `arcdata` 。|
|提供來源容器登錄的使用者名稱-請按 ENTER 鍵以使用 none：|SOURCE_DOCKER_USERNAME|如果您要從需要登入的來源提取容器映射，請只提供值。  Microsoft Container Registry 不需要登入。|
|提供來源容器登錄的密碼-請按 ENTER 鍵以使用 none：|SOURCE_DOCKER_PASSWORD|如果您要從需要登入的來源提取容器映射，請只提供值。  Microsoft Container Registry 不需要登入。 這是遮罩密碼提示。  如果您在中輸入或貼上密碼，則不會看到密碼。|
|在來源提供映射的容器映射標籤-按 ENTER 鍵以使用 ' `<current monthly release tag>` '：|SOURCE_DOCKER_TAG|預設的標記名稱會每月更新，以反映 Microsoft Container Registry 上目前版本的月份和年份。|
|提供目標容器登錄 DNS 名稱或 IP 位址：|TARGET_DOCKER_REGISTRY|目標登錄 DNS 名稱或 IP 位址。  這是要將映射推送 _至_其中的登錄。|
|提供目標 container registry 存放庫：|TARGET_DOCKER_REPOSITORY|要將映射推送至目標登錄上的存放庫。|
|提供目標容器登錄的使用者名稱-請按 enter 鍵以使用 none：|TARGET_DOCKER_USERNAME|用來登入目標容器登錄的使用者名稱（如果有的話）。|
|提供目標容器登錄的密碼-請按 enter 鍵以使用 none：|TARGET_DOCKER_PASSWORD|用來登入目標容器登錄的密碼（如果有的話）。 這是遮罩密碼提示。  如果您在中輸入或貼上密碼，則不會看到密碼。|
|提供目標映射的容器映射標記：|TARGET_DOCKER_TAG|一般而言，您會使用與來源相同的標記來避免混淆。|