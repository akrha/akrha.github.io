# akrha's Page

https://akrha.github.io/ のソース（Jekyll + [minima](https://github.com/jekyll/minima) テーマ）。

## ローカルでプレビューする

Ruby はローカルに入れず、Docker で動かす（macOS / Linux 共通）。
必要なのは Docker と Docker Compose v2 だけ。

```sh
docker compose up
```

- http://localhost:4000 を開く
- ファイルを保存すると自動で再ビルドされ、ブラウザもリロードされる（LiveReload）
- 止めるときは `Ctrl+C`。コンテナまで消すなら `docker compose down`

初回は Ruby イメージの取得と gem のインストールで数分かかる。gem は Docker の volume（`bundle`）にキャッシュされるので、2 回目からはすぐ起動する。

### よく使うコマンド

| やりたいこと | コマンド |
|---|---|
| バックグラウンドで起動 | `docker compose up -d` |
| ログを見る | `docker compose logs -f` |
| ビルドだけ（`_site/` に出力） | `docker compose run --rm jekyll sh -c "bundle install && bundle exec jekyll build"` |
| gem を更新（Gemfile.lock を更新） | `docker compose run --rm jekyll bundle update` |
| gem キャッシュを消して作り直す | `docker compose down -v` してから `docker compose up` |

### うまく動かないとき

- **ブラウザが空白ページ / `NS_ERROR_NET_EMPTY_RESPONSE` になる**: まだ起動途中。`docker compose logs -f` で `Server running...` が出るまで待ってからリロードする（初回は gem のインストールで 1〜2 分かかる）
- **`_config.yml` を変えたのに反映されない**: `_config.yml` は自動再読み込みされないので、`docker compose restart` する
- **ポート 4000 / 35729 が使用中**: 別の `jekyll serve` などを止めるか、`compose.yaml` の `ports` の左側を変える
- **Linux で `_site/` が root 所有になる**: `sudo rm -rf _site .jekyll-cache` で消してよい（どちらも `.gitignore` 済み）

## ファイル構成

| ファイル | 役割 |
|---|---|
| `_config.yml` | サイト設定（タイトル、テーマ、SNS リンク） |
| `index.md` | トップページ |
| `Gemfile` / `Gemfile.lock` | Jekyll とプラグインのバージョン |
| `compose.yaml` | ローカルプレビュー用の Docker 設定 |
