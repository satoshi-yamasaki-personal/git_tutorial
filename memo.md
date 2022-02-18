# gitの初期設定(global)
## ファイルのありか
> ~/.gitconfig

## 各種値の設定
- username, email, editorを設定
> git config --global user.name ["user-name"]
> git config --global user.email [email]
> git config --global core.editor [code --wait]
> ※ VSCodeを設定

## 設定値の確認
> git config --list
> git config [component]

# gitの操作の流れ
- git add: ワークツリー -> ステージ
  ※ repositoryに圧縮ファイル、stageにインデックス(圧縮ファイルと実際のファイルのマッピング)が生成
- git commit: ステージ -> リポジトリ(この時点のスナップショットを都度保管)
  ※ repositoryにツリー(ファイル構成)とコミット(誰がいつコミットしたかの履歴)を作成(2回目以降はparentを保持)

## ローカルリポジトリの新規作成
> git init
-> .gitディレクトリ(=ローカルリポジトリ)が作成され、リポジトリ、圧縮ファイル、ツリーファイル、コミットファイル、インデックスファイル、設定ファイルetc...が生成

## GitHub上のプロジェクトから作成
> git clone [repository-url]

## 変更をステージに追加(add)
- git add [file | directory | .]
  ※'.'の場合は全て追加

## 変更をリポジトリに追加(commit)
- git commit [-m | -v]
  ※ -m: メッセージ付きで記録
    -v: エディタが起動し、変更内容を確認できる(事前に確認したい場合に使う。割とよく使う。)
    コメントは1行で書く or 1行目に要約/2行目を空白/3行目に理由を書く

## 差分を確認
- git status: ワークツリー、ステージ、リポジトリ間の差分ファイルを表示
- git diff [file]: git addする前の変更差分を表示
- git diff --staged: git addした後の変更差分を表示

## 変更履歴を確認
- git log: 変更履歴を表示
- git log --oneline: 1行で表示
- git log --oneline --decorate: これが一番わかりやすい(各ブランチがどのコミットを指してるかがわかる)
- git log -p [file]: 特定ファイルの変更差分を表示
- git log -n [num]: コミット数を制限して表示

## ファイルの削除を記録
- git rm [file]: ファイルごと削除
- git rm -r [dir]: ディレクトリごと削除
- git rm --cached [file]: ファイルを残して削除履歴だけ記録(パスワードが入ったファイルとか)

## 削除ファイルの復活を記録
- git reset HEAD [file]: 
  ※ リポジトリからのみ削除の場合はこれだけで復活
- git checkout [file]: 

## ファイルの移動を記録
- git mv [old_file] [new_file]: ファイル名変更(ステージに記録)

## GitHubにプッシュ
- git remote add origin https://github.com/[user]/[repository].git: originという名前でリモートリポジトリ(GitHub)を新規追加
  ※ originがない場合、毎回URLを指定する必要がある。originで指定しておくとgit cloneの時にこのリポジトリを取得する
- git push [remote] [branch]: ローカルリポジトリの内容をリモートリポジトリに反映
  ※ git push origin masterみたいな感じ
    初回はgit push -u origin masterとやっておくと、次回以降の入力が楽になる
- 事前準備としてAccess Token生成が必要
  ※ User > Setttings > Developer settings > Personal access tokens
- リポジトリはGUIから作成しておく
  ※ User > Your Profile > Repositories > New

## コマンドにエイリアスをつける
※ 下記はあくまでサンプルなので、実際の付け方は自由
- git config --global alias.ci commit
- git config --global alias.st status
- git config --global alias.br branch
- git config --global alias.co checkout
  ※ --globalは~/.gitconfigを更新、ない場合はproject/.git/configを更新

## 管理しないファイルをGitの管理から外す
- 自動生成されるファイルやパスワードなどが入ったファイルは除外すべき
- .gitignoreファイルを作成して記載

# ファイルへの変更を取り消す
## ワークツリーのファイルを元の状態に戻す
- git checkout -- [file | directory | .]
  ※ '--'はブランチ名とファイル名が被った場合に明示的にファイルなどを指定するために入力
    実態としてはstageの状態を取得してworktreeに反映

## ステージに追加した変更を取り消す(add取り消し)
- git reset HEAD [file | directory | .]
  ※ ワークツリーの変更は取り消されない。ワークツリーもリセットしたい場合は前述のgit checkoutを実行
    実態としてはrepositoryの状態を取得してstageに反映
    HEAD = 最新のcommit

## リポジトリに追加した変更を取り消す(commit取り消し)
- git commit --amend
  ※ 現在のstageの状態でcommitを作り直す
    Pushしたものはやり直しNG(pullされてるとズレるから)

# GitHubとやり取りする
## リモートリポジトリの情報を確認
- git remote: リモートリポジトリの一覧を表示
- git remote -v: 対応するリモートリポジトリのURLを表示
- git remote show [remote]: さらに多くの情報(push先、ブランチなども表示される)

## リモートリポジトリを追加
- リモートリポジトリは複数登録できる(チームごとに立てる、個人用に立てる)
- git remote add [name] [url]
  ※ 今回はgit remote add bak [url]で追加

## リモートリポジトリから情報を取得
- fetch: ローカルリポジトリに取得(ワークツリーは更新しない)
  ※ メインはこっち。pullは挙動が特殊(現在のワークツリーのブランチに対してpullしたブランチの情報がマージされる)なので注意が必要
- git fetch [remote]
  ※ 今回はgit fetch origin
- git mergeでワークツリーにも反映
- pull: fetch + mergeを一括処理
- git pull [remote] [branch]
  ※ 今回はgit pull origin master

- git fetch origin: originをローカルリポジトリに反映
- git branch -a: ブランチ一覧表示(master, remotes/*とかは全てブランチ)
- git checkout remotes/origin/master: remotes/origin/masterにブランチ切り替え
- git checkout master: masterにブランチ切り替え
- git merge origin/master: masterをワークツリーに反映(修正中のファイルは差分として残る)

## リモートリポジトリを修正/ 削除
- git remote rename [old] [new]: リモートリポジトリ名変更
- git remote rm [remote]: リモートリポジトリ削除


# ブランチ
## ブランチとは
- ブランチはコミットへのポインタ
- HEADはブランチへの参照 = どこのコミットかを指している

## 新しいブランチの作成
- git branch [branch-name]
  ※ git branch featureとか。あくまで作成だけで切り替えはしない
- git branch: 今あるブランチの一覧を表示(ローカルリポジトリのみ)
  ※ -aをつけるとリモートリポジトリのブランチも表示

## ブランチの切り替え
- git checkout [branch-name]: 切り替え
  ※ 切り替えるとワークツリーの中身も変わる
- git checkout -b [branch-name]: 新規作成 + 切り替え

## 変更のマージ
- git merge [branch]: masterにfeatureをマージするみたいな
- git merge [remote]/[branch]
  ※ git merge origin/master

### マージの種類
- Fast Forward: 早送りになるマージ(ブランチが枝分かれしてない場合はポイントを前に進めるだけ)
- Auto Merge: 基本的なマージ(それぞれの内容をマージして新たなコミットを作成。parentが複数ある)
- Conflict: 

## コンフリクトの解決
- 同じファイルの同じ行に対して異なる編集を行った時に発生。
- git merge後にコンフリクトの旨が表示されるので、git statusで対象を確認
- 対象ファイルを修正して再度コミット

### コンフリクトを起こさないために
- 同じファイルを複数人で変更しない
- pullやmergeをする前に変更中の状態をなくしておく(commitやstashしておく)
- pullするときはブランチを確認する(ブランチ混線しないように！)
- コンフリクトしても慌てない！

