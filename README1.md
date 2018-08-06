anacondaセットアップ
  
    python仮想環境構築(anaconda)
        conda create -n NAME

    仮想環境切り替え
        Windowsの場合
            activate NAME
        macOS, Linuxの場合
            source activate NAME

    仮想環境の終了
        deactivate

    仮想環境名のリスト表示
        conda info -e
    
    現在の仮想環境のパッケージ全リスト
        conda list

    仮想環境NAMEのパッケージ全リスト
        conda list -n NAME

    仮想環境の削除
        conda remove -n NAME --all


Djangoセットアップ
    
    Djangoインストール
        conda install django

    Djangoプロジェクトの作成
        django-admin startproject NAME

    起動
        python manage.py runserver

    マイグレーション
        一番最初に権限周りのデータをマイグレイト
            python manage.py migrate
        
    マイグレーション
        新しい定義ファイルmodelを検知してマイグレイト
            python manage.py makemigrations
            python manage.py migrate
        
    アプリケーションの追加
        python manage.py startapp NAME
        上記実行後settings.pyのINSTALLED_APPSに参照させたいクラスを追加する

        
    管理者ユーザー作成
        python manage.py createsuperuser


PyCharmのセットアップ
    
    [ctrl] + [alt] + s で設定viewを開く。
    ProjectInspectorを今回作成したPython仮想環境のパスを指定する。
    エディターが仮想環境にインストールしたライブラリも認識できるようになる。

その他必要なライブラリ
    
    pillow(画像処理)
        pip install pillow
        
sqlite3のコマンド操作

    sqlite3に入る
        sqlite3 db,sqlite3
        
    テーブル一覧表示
        .tables
        
git操作で悩んだこと

    .gitignoreを後から追加する
        1.  .gitignoreファイルを作成する
        2.  ファイル内にリポジトリから除外したいファイル、ディレクトリを追記
        3.  .gitignoreファイルの状況から除外対象ファイルを一覧表示
                git ls-files --full-name -i --exclude-from=.gitignore
        4.  ローカルリポジトリから除外対象を削除
                やり方①(手入力多め)
                    git rm -r --cached ディレクトリ名orファイル名
                やり方②(.ignoreファイルの内容を削除対象にする)
                     git rm --cached `git ls-files --full-name -i --exclude-from=.gitignore`
        5.  .gitignoreファイルをadd
                git add .gitignore
        6.  .gitignoreファイルと削除情報をコミット
                git commit
        7.  リモートリポジトリへpush
                git push
                
    ローカルをリモートリポジトリの状態に強制的に合わせる。
        リモートの更新情報をとってくる
            git fetch --all
            
        リモートの状態に強制上書き
            git reset --hard origin/master