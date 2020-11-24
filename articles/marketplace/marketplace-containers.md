---
title: Azure Marketplace 中的容器供應專案發佈指南
description: 本文說明在 Azure Marketplace 中發佈容器供應專案的需求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95741656"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Azure container 優惠的發佈指南

Azure 容器供應專案可協助您將容器映射發佈至 Azure Marketplace。 您可以使用本指南來瞭解此供應專案類型的需求。

Azure 容器供應專案是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者看到的清單選項是「立即取得」。

當您的解決方案是設定為以 Kubernetes 為基礎之 Azure 容器實例的 Docker 容器映射時，請使用 Azure 容器供應專案類型。

> [!NOTE]
> Azure 容器實例是一個執行時間 docker 實例，可提供在 Azure 中執行容器最快速且最簡單的方式，而不需要管理任何虛擬機器，也不需要採用更高層級的服務。 容器實例可以直接部署至 Azure，或透過 Azure Kubernetes Services 或 Azure Kubernetes Service 引擎進行協調。  

Microsoft 目前支援免費和自備授權 (BYOL) 授權模型。

## <a name="container-offer-requirements"></a>容器供應項目需求

| 需求 | 詳細資料 |  
|:--- |:--- |  
| 計費和計量 | 支援免費或 BYOL 計費模型。<br><br> |  
| 從 Dockerfile 建立的映射 | 容器映射必須以 Docker 映射規格為基礎，而且是從 Dockerfile 所建立。<br> <br>如需有關建立 Docker 映射的詳細資訊，請參閱 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/#usage)的「使用方式」一節。<br><br> |  
| 在 Azure Container Registry 存放庫中裝載 | 容器映射必須裝載于 Azure Container Registry 存放庫中。<br> <br>如需有關使用 Azure Container Registry 的詳細資訊，請參閱 [快速入門：使用 Azure 入口網站建立私用容器](../container-registry/container-registry-get-started-portal.md)登錄。<br><br> |  
| 影像標記 | 容器映射必須包含至少一個標記 (標籤數目上限： 16) 。<br><br>如需有關標記映射的詳細資訊，請參閱 `docker tag` [Docker 檔](https://docs.docker.com/engine/reference/commandline/tag) 網站上的頁面。<br><br> |  

## <a name="next-steps"></a>後續步驟

- 若要準備容器供應專案的技術資產，請參閱 [建立 Azure 容器技術資產](create-azure-container-technical-assets.md)。

- 若要建立 Azure 容器供應專案，請參閱 [Azure Marketplace 中的建立 azure 容器供應](create-azure-container-offer.md) 專案，以取得詳細資訊。
