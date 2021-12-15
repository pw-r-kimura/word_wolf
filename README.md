# ワードウルフアプリ

## 条件

- 機能
  - 複数人でワードウルフができる
  - リアルタイム通信(ActionCable)
  - 今後もゲームを追加していくことを想定する
  - ログインしなくてもできるようにする(ゲストみたいな感じ)
- DB: MYSQL
- framework: Ruby on Rails

## アーキテクチャ

### 実行環境

- CentOS Linux release 8.3.2011
- rails 7.0.1.rc
- ruby 3.0.3
- mysql 8.0.26

### DB

- schema : ww_app_development(開発用), ww_app_test(テスト用)
- table
  - users
    - columns
      - id primary
      - name:string
      - 参加ルーム番号(room_id):integer
      - 部屋長かどうか？(room_owner):boolen
  - games
    - columns
      - id primary
      - ゲーム名(name):string
      - カテゴリ名(kategory):string
      
  - wordwolfs(お題)
    - columns
    
      -　 idprimary
      - 多数派お題(major_theme):string → index にして uniqueキーにする 「theme」
      - 少数派お題(minor_theme):string → index にして　uniqueキーにする  「theme」
      - 多数派の人数(major_count):integer
      - 少数派の人数(minor_count):integer
      - カテゴリ名(kategory):string
      - 制限時間(limit_time):integer
  - rooms
    - columns
      - id primary
      - ルーム参加人数(count):integer

### MVCの動き

- パス
  - /games
    - 一覧画面 (get　'/games',games#index)
    - ゲーム画面 (get '/games/:id' ,games#show) ← ここでリアルタイム実装する & ログインしていないとログイン画面に飛ばすようにする
  - /login
    - ゲストログイン画面 (get　'/login', sessions#new) 
   
- 流れ（flowchart）
  - ゲーム一覧画面　
  - ゲーム選択(wordwolf)
  - (ここからをリアルタイムにしたい)ゲスト　or　ログイン
  - ルーム作成 or ルーム参加
  ↓以降の処理は、待機場所を抜けるまでループ
  - 待機場所（お題選択・制限時間・多数・少数の人数など）
    - 部屋オーナーだったら
      - ゲームを開始する
    - それ以外
      -　準備完了ボタン
  - ゲーム開始
    - お題表示
    - 時間になったら、試合終了
    - 結果を表示
  - 元のルームの待機場所に戻る
