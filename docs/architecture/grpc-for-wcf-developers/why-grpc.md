---
title: WCF 開発者に gRPC を推奨する理由 - WCF 開発者向け gRPC
description: gRPC が、最新のアーキテクチャとプラットフォームに移行する WCF 開発者に適している理由について説明します。
ms.date: 09/02/2019
ms.openlocfilehash: 664781e94c24c1796eafa6a70eb28d453b530d66
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2020
ms.locfileid: "79147647"
---
# <a name="why-we-recommend-grpc-for-wcf-developers"></a>WCF 開発者に gRPC を推奨する理由

gRPC の言語とテクニックについて詳しく説明する前に、.NET Core に移行する Windows コミュニケーション ファウンデーション (WCF) 開発者にとって gRPC が適切なソリューションである理由を説明する必要があります。

## <a name="similarity-to-wcf"></a>WCF との類似性

実装とアプローチは gRPC では異なりますが、gRPC を使用したサービスの開発と使用の経験は、WCF 開発者にとって直感的な操作性を備えている必要があります。 基本的な目標は、クライアントとサーバーが同じプラットフォーム上にあるかのようにコーディングできるように、ネットワークを気にすることなく、同じことです。

どちらのプラットフォームも、インターフェイスを宣言し、そのインターフェイスを実装する際のプロセスが異なっていても、インターフェイスを宣言してから実装するという原則を共有しています。 5 章で説明するように、gRPC がサポートするさまざまな種類の RPC 呼び出しは、WCF サービスで使用できるバインドに適したマップに対応しています。

## <a name="benefits-of-grpc"></a>gRPC の利点

gRPCは、次の理由で他のソリューションの上に立っています。

### <a name="performance"></a>パフォーマンス

HTTP/1.1 ではなく HTTP/2 を使用すると、人間が読めるメッセージの要件がなくなります。 これは、コンピューターの解析の方が効率的です。 HTTP/2 は、単一の接続での要求の多重化もサポートしています。 このサポートにより、応答はキューで待機する必要なく、準備が整ったらすぐに送信されます。 (HTTP/1.1 では、この問題は "ヘッド オブ ライン (HOL) ブロッキング" と呼ばれます。gRPC を使用する場合はリソースが少なくて済むため、モバイル デバイスや低速ネットワークで使用するソリューションとして適しています。

### <a name="interoperability"></a>相互運用性

.NET、Java、Python、Go、C++、Node.js、スウィフト、ダート、ルビー、PHPなど、すべての主要なプログラミング言語とプラットフォーム用のgRPCツールとライブラリがあります。 Protocol Buffers バイナリ ワイヤ形式と各プラットフォームの効率的なコード生成により、開発者は完全なクロスプラットフォーム サポートを利用しながら、パフォーマンスの高いアプリを構築できます。

### <a name="usability-and-productivity"></a>使いやすさと生産性

gRPC は包括的な RPC ソリューションです。 複数の言語とプラットフォームで一貫して動作します。 また、必要な定型コードの多くが自動的に生成され、優れたツールを提供します。 そのため、ビジネス ロジックに集中するために、開発者の時間が増えます。

### <a name="streaming"></a>ストリーミング

gRPC には、WCF の全二重サービスと同様の機能を提供する、双方向の全方向ストリーミングがあります。 gRPC ストリーミングは、通常のインターネット接続、ロード バランサー、サービス メッシュを介して動作できます。

### <a name="deadlinetimeouts-and-cancellation"></a>締切/タイムアウトとキャンセル

gRPC を使用すると、クライアントは RPC が終了するまでの最大時間を指定できます。 指定された期限を超えた場合、サーバーはクライアントとは別に操作を取り消すことができます。 期限と取り消しは、リソースの使用制限を強制するために、さらに gRPC 呼び出しを通じて伝達できます。 クライアントは、期限を超えた場合や、必要に応じて (たとえば、ユーザー操作が原因で) 操作を停止することもできます。

### <a name="security"></a>Security

gRPC は、TLS エンドツーエンドの暗号化接続で HTTP/2 を使用している場合、暗黙的にセキュリティで保護されます。 クライアント証明書認証のサポート ([第 6 章](security.md)を参照) により、クライアントとサーバー間のセキュリティと信頼がさらに強化されます。

>[!div class="step-by-step"]
>[前次](network-protocols.md)
>[Next](protocol-buffers.md)
