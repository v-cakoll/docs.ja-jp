---
title: openSUSE に .NET Core をインストールする - .NET Core
description: openSUSE に .NET Core SDK と .NET Core ランタイムをインストールするさまざまな方法を示します。
author: adegeo
ms.author: adegeo
ms.date: 06/04/2020
ms.openlocfilehash: 24f0a5b5278d038c2f941b0984efcacd91dcbe31
ms.sourcegitcommit: e02d17b2cf9c1258dadda4810a5e6072a0089aee
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85619469"
---
# <a name="install-net-core-sdk-or-net-core-runtime-on-opensuse"></a>openSUSE に .NET Core SDK または .NET Core ランタイムをインストールする

.NET Core は openSUSE でサポートされています。 この記事では、openSUSE に .NET Core をインストールする方法について説明します。

[!INCLUDE [linux-intro-sdk-vs-runtime](includes/linux-intro-sdk-vs-runtime.md)]

[!INCLUDE [linux-install-package-manager-x64-vs-arm](includes/linux-install-package-manager-x64-vs-arm.md)]

## <a name="supported-distributions"></a>サポートされているディストリビューション

次の表は、openSUSE 15 で現在サポートされている .NET Core リリースの一覧です。 これらのバージョンは、[.NET Core のバージョンがサポート終了になる](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)か、openSUSE のバージョンがサポート終了になるまでサポートされます。

- ✔️ は、openSUSE または .NET Core のバージョンがまだサポートされていることを示します。
- ❌ は、openSUSE または .NET Core のバージョンがその openSUSE のリリースではサポートされていないことを示しています。
- openSUSE のバージョンと .NET Core のバージョンの両方に ✔️ が付いている場合、その OS と .NET の組み合わせはサポートされています。

| openSUSE                   | .NET Core 2.1 | .NET Core 3.1 | .NET 5 Preview (手動インストールのみ) |
|----------------------------|---------------|---------------|----------------|
| ✔️ [15](#opensuse-15-)     | ✔️ 2.1        | ✔️ 3.1        | ✔️ 5.0 Preview |

次のバージョンの .NET Core は、サポート対象外となりました。 これらのダウンロードは、まだ公開されています。

- 3.0
- 2.2
- 2.0

## <a name="how-to-install-other-versions"></a>その他のバージョンをインストールする方法

[!INCLUDE [package-manager-switcher](./includes/package-manager-heading-hack-pkgname.md)]

## <a name="opensuse-15-"></a>openSUSE 15 ✔️

[!INCLUDE [linux-prep-intro-generic](includes/linux-prep-intro-generic.md)]

```bash
sudo zypper install libicu
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
wget https://packages.microsoft.com/config/opensuse/15/prod.repo
sudo mv prod.repo /etc/zypp/repos.d/microsoft-prod.repo
sudo chown root:root /etc/zypp/repos.d/microsoft-prod.repo
```

[!INCLUDE [linux-zyp-install-31](includes/linux-install-31-zyp.md)]

## <a name="troubleshoot-the-package-manager"></a>パッケージ マネージャーのトラブルシューティング

このセクションでは、パッケージ マネージャーを使用して .NET Core をインストールするときに発生する可能性のある一般的なエラーについて説明します。

### <a name="failed-to-fetch"></a>フェッチできない

[!INCLUDE [package-manager-failed-to-fetch-rpm](includes/package-manager-failed-to-fetch-rpm.md)]

## <a name="snap"></a>Snap

[!INCLUDE [linux-install-snap](includes/linux-install-snap.md)]

## <a name="dependencies"></a>依存関係

パッケージ マネージャーを使用してインストールする場合、次のライブラリが自動的にインストールされます。 ただし、手動で .NET Core をインストールする場合、または自己完結型アプリを公開する場合は、次のライブラリがインストールされていることを確認する必要があります。

- krb5
- libicu
- libopenssl1_0_0

ターゲット ランタイム環境の OpenSSL バージョンが 1.1 以降である場合は、**compat-openssl10** をインストールする必要があります。

依存関係の詳細については、「[Self-contained Linux applications](https://github.com/dotnet/core/blob/master/Documentation/self-contained-linux-apps.md)」(自己完結型 Linux アプリケーション) をご覧ください。

*System.Drawing.Common* アセンブリを使用する .NET Core アプリの場合は、次の依存関係も必要です。

- [libgdiplus (バージョン 6.0.1 以降)](https://www.mono-project.com/docs/gui/libgdiplus/)

  > [!WARNING]
  > 最新バージョンの *libgdiplus* をインストールするには、システムに Mono リポジトリを追加します。 詳細については、「<https://www.mono-project.com/download/stable/>」を参照してください。

## <a name="scripted-install"></a>スクリプトでのインストール

[!INCLUDE [linux-install-scripted](includes/linux-install-scripted.md)]

## <a name="manual-install"></a>手動インストール

[!INCLUDE [linux-install-manual](includes/linux-install-manual.md)]

## <a name="next-steps"></a>次の手順

- [チュートリアル: Visual Studio Code を使用して .NET Core SDK でコンソール アプリケーションを作成する](../tutorials/with-visual-studio-code.md)
