テストの目的

- 品質を担保して、価値提供をすること
- テストを成功させることが目的になってはいけない
- CI の黄金律は、「クリーンに保つ」「高速に実行する」「ノイズを減らす」

シグナルとノイズ

- シグナル：価値のある情報
- ノイズ：注意を逸らす情報（無価値）

ワークフローの基本設計

- チェックアウト・言語セットアップ・テストの順番

フレーキーテスト

- テストが通ったり、通らなかったりする現象
- 通常であればテストが通ることに賭けて、再度実行する人が多いが、これは確実にやっていはいけない方法
- 問題が確実に発生しているので、時間をかけてでも解消する
- テストが通ることが目的になっているが、本来はテストを通して有益なフィードバックを得ることが目的

フラジャイルテスト

- リファクタリングをした際に大量にテストが失敗する現象
- リファクタは本来の挙動は変えずに、内部のロジックを正すことだが、テストが失敗することはないはず
- 単純にテストコードの実装方法に問題があるため、設計を考えることが重要

スローテスト

- 実行にどうしても時間がかかってしまうテスト
- 色々な案が考えられる
  - そもそも対象から外す：妥協案だが、状況に応じて採用することも必要
  - 定期実行する：毎回 CI テストを走らせるのではなく、定期的に実行する
  - merge に実行する

テスト実行時の戦略

- 部分実行：テストの一部だけを実行する
- 並列実行：複数のテストを同時実行できる
- シャッフル実行：ランダムでテスト実行する
  - そもそもテストケースは独立しているべきであり、依存関係があってはいけない
  - シャッフル実行で依存関係があるテストをあぶり出せる
- カテゴリ実行：カテゴリごとにテストをまとめることで、状況に応じて実行できる

重要な情報を見落とさない工夫

- ロギング：ワークフロー実行中に起きたことをトレース
- レポーティング：重要度の高い情報へ簡単にアクセス
- チャット通知：リアルタイムに通知して、なんらかの行動を促す

ジョブ管理

- 複数ジョブの並列実行：ジョブの並列実行と逐次実行
- マトリックス：類似したジョブの効率的な実装
- Environments：異なる環境向けのジョブ実行

データ管理

- キャッシュ：複数ワークフローでデータを再利用
- アーティファクト：ワークフロー実行後もデータを保管

デバックログ

- ワークフロー再実行時に「Enable debug logging」を有効化する
- variables, secrets に値を設定
- ステップデバッグログとランナー診断ログの 2 種類がある
  - ステップごとの詳細なログが見れる：secrets, variables に「ACTIONS_STEP_DEBUG」True で設定する
  - ランナーの詳細なログが見れる： secrets, variables に「ACTIONS_RUNNER_DEBUG」True で設定する
    - web からは参照できないので、詳細ページからログファイルをダウンロードする必要がある

ワークフローコマンド

- echo コマンド経由でランナーに対して特別な操作ができる

```
::workflow-command parameter1=<data1>, parameter2=<data2>::<command value>
```

Bash トレーシング

- 通常のデバックログは流量が多いので、よりシンプルにできる

```
set -x
```

ログのグループ化

- ログを意味のある単位にまとめる

```
::group::<group-name>
::endgroup::
```

ログの手動マスク

- secrets の情報は自動でマスクされるが、手動でマスクすることもできる

アノテーション

- ラベルをつけることができる

```
steps:
    - run: echo "::error::This is an error"
    - run: echo "::warning::This is an warning"
    - run: echo "::notice::This is an notice"
```

ジョブサマリー
- テーブルやリスト・メトリクスなどの情報量の多い出力を可能にする

チャット通知の設定
- チャットサービス側で設定
  - slack, Teamsでワークスペースを作成し、Github連携し、特定のスラッシュコマンドをワークスペースに投げるだけで完了
  ```
  /github subscribe <OWNER>/<REPO> workflows
  ```
- チャットサービスが提供するアクションを利用
  - カスタマイズ性があり、通知タイミングなどを自由に設定できる
  ```
  slackapi/slack-github-action
  ```