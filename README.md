anacondaセットアップ
  
    python仮想環境構築(anaconda)
        conda create -n NAME

    仮想環境切り替え
        activate NAME  <-Windows
        source activate NAME <-macOS, Linux

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

    マイグレーション(一番最初に権限周りのデータをマイグレイト)
        python manage.py migrate

    アプリケーションの追加
        python manage.py startapp NAME
        ->settings.py内INSTALLED_APPSに参照させたいクラスを追加する


PyCharmのセットアップ
    
    [ctrl] + [alt] + s で設定viewを開いてProjectInspectorを今回作成したPython仮想環境のパスを指定する
    ->エディターが仮想環境にインストールしたライブラリも認識できるようになる。
