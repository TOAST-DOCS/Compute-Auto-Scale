## Compute > Auto Scale > 概要

オートスケール(Auto Scale)は、インスタンスの負荷を常にモニタリングして、必要に応じてインスタンスを追加作成または削除し、個別のインスタンスにネットワーク遮断などの障害が発生した場合、自動で新しいインスタンスを作成し、障害が発生したインスタンスを置き換えるサービスです。オートスケールを通して負荷にリアルタイムで対応し、安定的かつ適切なサービスを提供できます。

## スケーリンググループ
スケーリンググループはインスタンスを追加で生成または削除する条件と、条件を満たす場合に実行する動作を定義したものです。スケーリンググループは最小、最大インスタンス数、初期駆動させるインスタンス数、および増設、縮小(scale out, scale in)ポリシーで構成されます。

### 最小、最大、駆動インスタンス
最小、最大、駆動インスタンスはスケーリンググループで必ず定義する必要があるパラメータで、意味は次のとおりです。

- 最小、最大インスタンス：スケーリンググループが生成できるインスタンス数の最小値と最大値です。
- 駆動インスタンス：スケーリンググループが生成し、駆動中のインスタンスの数です。この値が変更されると、インスタンスの数が調整されます。

最小、最大、駆動インスタンスがそれぞれ1, 10, 2の場合、最初は駆動インスタンスによって2つのインスタンスが生成されます。そして負荷に応じて増設または縮小ポリシーが発動すると、駆動インスタンスの台数がポリシーに応じて増減します。駆動インスタンスは、どのような状況でも最小、最大インスタンスに指定された範囲を超えられません。

### ポリシー
ポリシーとは、インスタンスの台数の増設や削減の基準を定義したものです。1つ以上の条件と、条件を満たした時の動作で構成されます。
ポリシーはインスタンス性能指標、基準値、持続時間というような条件で構成されます。スケーリンググループで使用するインスタンス性能指標は次のとおりです。

- CPU使用率(%)
- メモリ使用率(%)
- ディスクの1秒当たりの読み取り/書き込み量(Bytes/s)
- ネットワークの1秒当たりの送信/受信量(Bytes/s)

スケーリンググループは上記の性能指標を1分周期で収集します。収集した性能指標はスケーリンググループ単位で管理されます。最後に、オートスケールサービスが確認する値は、スケーリンググループ内の全てのインスタンスの平均値です。

> [注意]特定インスタンスだけの条件を満たしている場合はポリシーが発動しません。
>
> スケーリンググループは、指定された性能指標が持続時間中に基準値を超えるかを継続的に監視して、ポリシーを発動させるかどうかを決定します。例えば、条件がCPU使用率90%以上、持続時間が5分の場合、5分間CPU使用率が90%以下に下がらなければポリシーが発動します。

指定された条件を満たしてポリシーが発動する場合、スケーリンググループは条件に応じた動作を実行します。動作とはスケーリンググループの駆動するインスタンスの台数を調整することです。駆動インスタンスの台数が増加すると*増設ポリシー**、削減すると*縮小ポリシー**と呼びます。

> [参考]縮小ポリシーが発動すると、最も古いインスタンスから停止します。インスタンスが停止する時に発生するシグナル（Linuxの場合［SIGTERM/SIGKILL](https：//www.freedesktop.org/software/systemd/man/systemd.service.html), Windowsの場合［WM_QUERYENDSESSION](https：//msdn.microsoft.com/en-us/library/windows/desktop/aa376890.aspx)/[WM_ENDSESSION](https：//msdn.microsoft.com/en-us/library/windows/desktop/aa376889.aspx))のハンドラを実装して、サービスが中断することなく終了できるようにします。

条件に合う動作を無制限に実行することを防ぐために、再使用待機時間（cooldown time)を設定します。最後に動作を実行した時から再使用待機時間の間は条件を満たしてもポリシーを発動しません。

再使用待機時間の意味を説明するため、例を挙げます。

まず増設ポリシーが _「CPU使用率90%以上、5分間持続の場合、駆動インスタンス2つ増加」_ だと仮定します。もし実際のインスタンスのCPU使用率90%以上を6分間継続した場合、最初の5分経過後、インスタンスを2つ生成します。次に1分が過ぎるとCPU使用率90%以上、5分間持続の条件をもう一度満たすので再びインスタンスを2つ生成します。

このように再使用待機時間がない場合、予期せぬインスタンスの増減が発生することになります。効率的なリソース活用のために適切な再使用待機時間を設定してください。

> [参考]スケーリンググループで増設ポリシーと縮小ポリシーの再使用待機時間をそれぞれ指定できます。
> 通常、増設ポリシーの再使用待機時間は突然の負荷増加に対応できるようにできるだけ短くしておき、縮小ポリシーの再使用待機時間はゆっくりインスタンスをサービスから除外するようにできるだけ長くします。サービスの負荷状況をしっかり監視して適切なポリシーを設定すると、インスタンスが浪費されることを防ぐことができます。

<br>

> [注意]増設/縮小ポリシーが同時に発動することを防ぐため、スケーリンググループにも再使用待機時間があります。スケーリンググループの再使用待機時間は増設、縮小ポリシーの再使用待機時間のうち、小さい値になります。

自動復旧ポリシーを使用すると、個別インスタンスに障害が発生した時、そのインスタンスを削除し、新しいインスタンスを作成して置き換えます。個別インスタンスの性能指標が3分間収集されなかった場合、障害と判断して自動復旧が行われます。自動復旧は再使用待機時間に関係なく動作します。

### ロードバランサー
インスタンス生成後、接続するロードバランサーを指定します。増設ポリシーが発動した時、生成されたインスタンスは指定されたロードバランサーに接続します。新たに追加されたインスタンスにロードバランサーで自然に負荷を分散してサービスに即座に投入できます。

> [参考]実際に生成されたインスタンスがサービスに投入されるタイミングは、ブート完了後、サービスが開始されロードバランサーの状態確認に正常に応答した後です。

## 使用シナリオ
スケーリンググループは、設定されたポリシーに従って自動的にインスタンスを増設または減縮します。しかし必要に応じてユーザーが明示的に駆動インスタンス数を調整できます。オートスケールでサポートしている調整の種類は次のとおりです。

- ポリシーによる調整<br>
  スケーリンググループで指定したポリシーに従って、インスタンスの数を調整します。スケーリンググループの基本的な動作です。

- ユーザーによる調整<br>
  ユーザーがコンソールで増設、縮小ポリシーを発動する方式です。増設、縮小ポリシーの発動条件を満たさなくても実行されます。

- 予約による調整<br>
  インスタンスの性能指標を基準とする代わりに、特定のタイミングごとに駆動インスタンスの数を調整します。発動時間は1度だけ実行するように設定することもでき、周期的に繰り返し実行するよう設定することもできます。
