—
title: "D 言語のコンパイル方法とその種類"
emoji: "🌊"
type: "tech"
topics:
  - "dlang"
published: false
published_at: "2022-11-07 07:28"
—

## はじめに

最近 D 言語に興味を持ち，使いながら勉強しています．
学習中，D 言語には複数の種類のコンパイル方法が存在する事に気づきました．
初歩的な事だとは思いますが，少し混乱したので簡単にまとめます．
なお，本記事では D 言語の開発環境の構築方法については触れません．

## D 言語のコンパイラ・コンパイル方法の種類

- DMD
	- D 言語の開発元の DigitalMars が配布しているコンパイラ
	- リファレンス実装
	- コンパイルコマンド例: `rdmd main.d`
- LLVM D Compiler
	- LDC は，LLVM をベースに開発されたD言語処理
	- パフォーマンスを重視しており，DMD よりも最適化が効くらしい (実行が高速)
	- コンパイルコマンド例: `ldc2 main.d`
- DUB
	- パッケージマネージャ，ビルドツール
	- 必要なライブラリのリンク・ビルドに便利
	- 下記コマンド例でコンパイル・実行まで可能
	- コンパイルコマンド例: `dub main.d`
- GCC
	- GCC (GNU Compiler Collection) を D 言語用に拡張したコンパイラで， gcc-gdc や略して gdc と呼ばれる
	- コンパイルコマンド例: `gdc main.d`



参考: 

[D言語/インストールおよび実行方法](https://ja.wikibooks.org/wiki/D%E8%A8%80%E8%AA%9E/%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%8A%E3%82%88%E3%81%B3%E5%AE%9F%E8%A1%8C%E6%96%B9%E6%B3%95)

[DMD コンパイラ OSX 版](http://www.kmonos.net/alang/d/dmd-osx.html)

[Eilmer FAQs](https://gdtk.uqcloud.net/docs/eilmer/eilmer-faq-for-hugo/#_the_recommendation_is_for_the_latest_d_compiler_but_that_doesnt_play_nicely_with_glibc_on_the_system_what_can_i_do)

[Getting Started](https://wiki.dlang.org/Getting_Started)

[D言語でビルドツールDUBを用いて便利なライブラリをより簡単に利用する](https://qiita.com/yasei_no_otoko/items/2724eebab10f5cd0a02f)

## まとめ

- 基本的には，DMD コンパイラを使えば良い
	- コンパイルコマンド例: `rdmd main.d`
- 高速な実行速度が欲しい場合は，LDC コンパイラを使えば良い
	- コンパイルコマンド例: `ldc2 main.d`
- D 言語を用いたプロジェクトにはパッケージマネージャである DUB を使うと便利
