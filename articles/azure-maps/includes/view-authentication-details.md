---
title: 查看 Azure 地圖服務的驗證詳細資料
description: 使用 [Azure 入口網站來查看 Azure 地圖服務的驗證詳細資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126445"
---
您可以在 Azure 入口網站中查看 Azure 地圖服務帳戶驗證詳細資料。 在您的帳戶中，選取 [**設定**] 功能表上的 [**驗證**]。

![驗證詳細資料](../media/how-to-manage-authentication/how-to-view-auth.png)

建立 Azure 地圖服務帳戶之後，Azure 地圖服務 `x-ms-client-id` 值會出現在 [Azure 入口網站 authentication 詳細資料] 頁面中。 此值代表將用於 REST API 要求的帳戶。 這個值應該儲存在應用程式設定中，並在使用 Azure AD 驗證搭配 Azure 地圖服務時，于提出 HTTP 要求之前先行抓取。
