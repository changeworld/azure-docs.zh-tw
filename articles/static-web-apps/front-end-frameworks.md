---
title: 使用 Azure 靜態 Web Apps 預覽來設定前端架構
description: 適用于 Azure 靜態 Web Apps 所需的熱門前端架構設定
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976705"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>使用 Azure 靜態 Web Apps 預覽來設定前端架構和程式庫

Azure 靜態 Web Apps 要求您在前端架構或程式庫的[組建設定檔](github-actions-workflow.md)中擁有適當的設定值。

## <a name="configuration"></a>組態

下表列出一系列 framework 和程式庫的設定<sup>1</sup>。

資料表資料行的目的是由下列專案說明：

- **應用程式成品位置**：列出的值 `app_artifact_location` ，這是[應用程式檔的建立版本資料夾](github-actions-workflow.md#build-and-deploy)。

- **自訂群組建命令**：當架構需要的命令與或不同時 `npm run build` `npm run azure:build` ，您可以定義[自訂群組建命令](github-actions-workflow.md#custom-build-commands)。

| 架構 | 應用程式成品位置 | 自訂群組建命令 |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [角通用](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n/a |
| [Backbone.js](https://backbonejs.org/) | `/` | n/a |
| [Ember](https://emberjs.com/) | `dist` | n/a |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | n/a |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/a |
| [Hyperapp](https://hyperapp.dev/) | `/` | n/a |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/a |
| [jQuery](https://jquery.com/) | `/` | n/a |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n/a |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/a |
| [Marko](https://markojs.com/) | `public` | n/a |
| [Meteor](https://www.meteor.com/) | `bundle` | n/a |
| [Mithril](https://mithril.js.org/) | `dist` | n/a |
| [Polymer](https://www.polymer-project.org/) | `build/default` | n/a |
| [Preact](https://preactjs.com/) | `build` | n/a |
| [React](https://reactjs.org/) | `build` | n/a |
| [樣板](https://stenciljs.com/) | `www` | n/a |
| [Svelte](https://svelte.dev/) | `public` | n/a |
| [Three.js](https://threejs.org/) | `/` | n/a |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/a |
| [Vue](http://vuejs.com/) | `dist` | n/a |

<sup>1</sup>上表並非適用于 Azure 靜態 Web Apps 的完整架構和程式庫清單。

<sup>2</sup>不適用

## <a name="next-steps"></a>後續步驟

- [組建和工作流程設定](github-actions-workflow.md)
