---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748268"
---
FTP 和本機 Git 可以透過使用「部署使用者」  部署到 Azure Web 應用程式。 部署使用者只需設定一次，就能供所有 Azure 部署使用。 使用者名稱和密碼屬於帳戶等級部署，因此與 Azure 訂用帳戶認證不同。 

若要設定部署使用者，請在 Azure Cloud Shell 中執行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令。 以部署使用者的使用者名稱和密碼取代 \<username> 和 \<password>。 

- 使用者名稱在 Azure 服務及本機 Git 推送中都必須是唯一的，且不能包含 ‘\@’ 符號。 
- 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 輸出會將密碼顯示為 `null`。 如果您收到 `'Conflict'. Details: 409` 錯誤，請變更使用者名稱。 如果您收到 `'Bad Request'. Details: 400` 錯誤，請使用更強的密碼。 

將使用者名稱和密碼記錄下來，在部署 Web 應用程式時還會用到。
