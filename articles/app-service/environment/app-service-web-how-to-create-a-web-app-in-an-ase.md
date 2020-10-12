---
title: 在 ASE v1 中建立 web 應用程式
description: 瞭解如何在 App Service 環境 v1 中建立 web 應用程式。 本文件僅提供給使用舊版 v1 ASE 的客戶。
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d0ac8e0cf187d1a6a329f2c7332c9ec37018d4cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962514"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>在 App Service 環境 v1 中建立 Web 應用程式

> [!NOTE]
> 這篇文章是關於 App Service 環境 v1。  有較新版本的 App Service 環境，更易於使用，並且可以在功能更強大的基礎結構上執行。 若要深入瞭解新版本，請從 [App Service 環境簡介](intro.md)開始著手。
> 

## <a name="overview"></a>概觀
本教學課程說明如何在 [App Service 環境 v1](app-service-app-service-environment-intro.md) (ASE) 中建立 Web 應用程式和 App Service 方案。 

> [!NOTE]
> 如果您想要了解如何建立 Web 應用程式，但不需要在 App Service 環境中加以實作，請參閱 [建立 .NET Web 應用程式](../quickstart-dotnetcore.md) ，或其中一個適用於其他語言和架構的相關教學課程。
> 
> 

## <a name="prerequisites"></a>Prerequisites
本教學課程假設您已建立 App Service 環境。 如果尚未建立，請參閱 [建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。 

## <a name="create-a-web-app"></a>建立 Web 應用程式
1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [建立資源] > [Web + 行動] > [Web 應用程式]****。 
   
    ![顯示在 Azure 入口網站中建立 web 應用程式之位置的螢幕擷取畫面。][1]
2. 選取您的訂用帳戶。  
   
    如果您有多個訂用帳戶，請注意，若要在您的 App Service 環境中建立應用程式，必須使用您在建立環境時所使用訂用帳戶來建立。 
3. 選取或建立資源群組。
   
    *資源群組* 可讓您以一個單位的形式管理相關的 Azure 資源，並在為您的應用程式 * (azure RBAC) 規則建立 azure 角色型存取控制 * 時很有用。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀][ResourceGroups]。 
4. 選取或建立 App Service 方案。
   
    *App Service 規劃*是一組受控的 Web 應用程式。  當您選取價格時，支付的價格通常會套用到 App Service 方案，而非個別的應用程式。 在 ASE 中，您需對配置給 ASE 的計算執行個體付費，而不需對與您的 ASP 一起列出的項目付費。  若要相應增加 Web 應用程式的執行個體數目，您可相應增加 App Service 方案的執行個體，這會影響該方案中的所有 Web 應用程式。  方案中的某些功能 (例如網站位置或 VNET 整合) 也有數量限制。  如需詳細資訊，請參閱 [Azure App Service 計畫總覽](../overview-hosting-plans.md)
   
    您可以藉由查看方案名稱下加註的位置，來識別 ASE 中的 App Service 方案。  
   
    ![顯示的螢幕擷取畫面，可在您的 ASE 中查看 App Service 計畫。][5]
   
    如果您想要使用已存在於 App Service 環境中的 App Service 方案，請選取該方案。 如果您想要建立新的 App Service 方案，請參閱本教學課程的下一節： [在 App Service 環境中建立 App Service 方案](#createplan)。
5. 輸入 Web 應用程式的名稱，然後按一下 [建立] ****。 
   
    如果 ASE 使用外部 VIP，則 ASE 中應用程式的 URL 為：[*網站名稱*].[*App Service 環境的名稱*].p.azurewebsites.net，而非 [*網站名稱*].azurewebsites.net
   
    如果 ASE 使用內部 VIP，則該 ASE 中應用程式的 URL 為：[*網站名稱*].[*在 ASE 建立期間指定的子網域*]   
    在 ASE 建立期間選取您的 ASP 後，您會在 [名稱]**** 之下看到子網域更新

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> 建立 App Service 方案
當您在 App Service 環境中建立 App Service 方案時，您的背景工作角色選擇會因為在 ASE 中沒有共用的背景工作角色而有所不同。  您必須使用的背景工作角色是由系統管理員配置給 ASE 的背景工作角色。 這表示，若要建立新的方案，您需要將更多的背景工作配置給您的 ASE 背景工作集區，而不是在該背景工作集區中所有方案的實例總數。  如果您的 ASE 背景工作集區中沒有足夠的背景工作角色來建立方案，則您需要與 ASE 系統管理員合作來新增背景工作角色。

而 App Service 環境所裝載的 App Service 方案還有另一項差異，那就是缺少價格選取項目。  當您有 App Service 環境時，您會支付系統所使用的計算資源費用，但該環境中的方案不會有附加的費用。  當您建立 App Service 方案時，您通常會選取決定費率的價格方案。  App Service 環境基本上是您可以在其中建立內容的私人位置。  您可支付環境費用，而非裝載您的內容。

下列指示說明如何在您依照本教學課程的上一節所說明來建立 Web 應用程式時，建立 App Service 方案。

1. 在方案選取 UI 中按一下 [建立新項目] **** ，並提供方案的名稱，就跟您平常在 ASE 以外的地方所做的一樣。
2. 選取您想要從位置選擇器中使用的 ASE。
   
    因為 App Service 環境基本上是專用部署位置，所以它會顯示在 [位置] 下。 
   
    ![顯示位置選擇器的螢幕擷取畫面，用來選取您想要的 ASE。][2]
   
    在位置選擇器中選取 ASE 之後，App Service 方案建立 UI 將會更新。  位置現在會顯示 ASE 系統的位置及其所在區域，而背景工作集區選擇器會取代價格方案選擇器。  
   
    ![在位置選擇器中選取 ASE 之後，顯示 ASE 系統詳細資料的螢幕擷取畫面。][3]

### <a name="selecting-a-worker-pool"></a>選取背景工作集區
通常在 Azure App Service 中和 App Service 環境以外的地方，專用價格方案通常會有 3 種計算大小可供選擇。  同樣地，對於 ASE 您最多可以定義 3 個背景工作集區，並指定用於該背景工作集區的計算大小。  對 ASE 的租用戶來說，這代表租用戶並非根據 App Service 方案的計算大小來選取價格方案，而是選取所謂的「背景工作集區」 **。  

背景工作角色集區選取 UI 會在名稱下方顯示該背景工作集區角色使用的計算大小。  可用數量是指有多少運算執行個體可使用於該集區。  總計集區實際上可能有超過這個數字的執行個體，但這個值只是指未使用的數量。  如果您需要調整 App Service 環境以新增更多計算資源，請參閱 [設定 App Service 環境](app-service-web-configure-an-app-service-environment.md)。

![顯示 [背景工作集區] 窗格的螢幕擷取畫面，您可以在其中選取 ASE 的背景工作集區。][4]

在此範例中，您只會看到兩個可用的背景工作集區。 這是因為 ASE 系統管理員只會將主機配置到這兩個背景工作角色集區中。  第三個集區會在有 VM 配置到其中時出現。  

## <a name="after-web-app-creation"></a>建立 Web 應用程式之後
在 ASE 中執行 Web 應用程式及管理 App Service 方案時，有幾件事需要列入考量。  

如先前所述，ASE 的擁有者必須負責系統大小，因此他們也必須負責確保有足夠的容量來裝載所需的 App Service 方案。 如果沒有可用的背景工作角色，您將無法建立您的 App Service 方案。  也就無法相應增加您的 Web 應用程式。  如果您需要更多執行個體，您必須讓您的 App Service 環境的系統管理員新增更多的背景工作。

當您建立 Web 應用程式和 App Service 方案之後，最好能夠相應增加該方案的內容。  在 ASE 中，您一定需要至少 2 個 App Service 方案執行個體，才能為您的應用程式提供容錯功能。  在 ASE 中調整 App Service 方案的方式，就跟一般透過 App Service 方案 UI 來進行的方式相同。  如需關於調整的詳細資訊，請參閱 [如何在 App Service 環境中調整 Web 應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/?view=azps-3.8.0