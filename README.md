# sort.fumiproject.dev

条件で横断して比べるアグリゲーター（価格.com / マイベスト型）を、**単一ドメインのサブディレクトリ**に集約した公開用リポジトリ。

- 本番: https://sort.fumiproject.dev/
- ホスティング: GitHub Pages（`fumi3333/sort` の `main` ブランチ直下）
- DNS: Name.com に `CNAME sort → fumi3333.github.io`（TTL 300）
- このリポジトリは**ビルド成果物の置き場**。生成ロジックは各ジャンルのソース側にある。

## なぜサブドメインを分けずに集約したか

`resort-sort.fumiproject.dev` / `menkyo-sort.fumiproject.dev` のように分けると、
Google はそれぞれを別サイトとして扱うため、被リンクで得た評価が他ジャンルに一切回らない。

集客の主導線は「大学（ac.jp）の取材記事 → ポートフォリオ（works.fumiproject.dev）→ ここ」で、
入ってくる強い被リンクは1〜2本しかない。その数本を全ジャンルで共有するために、
サブディレクトリに寄せている。新ジャンルを足した初日から既存のドメイン評価が効く。

一方で、**各ジャンルのページからは他ジャンルへのリンクを出さない**。
利用者から見れば専門ツールのまま、裏側のドメイン評価だけを共有する、という設計。

## ディレクトリ

| パス | 中身 | ソース |
|---|---|---|
| `/` | ハブ（ポートフォリオからのリンク先） | `migrate_to_sort.py` の `HUB_INDEX` |
| `/resort/` | リゾートバイト横断検索（250ファイル） | `C:\resort-sort` |
| `/menkyo/` | 合宿免許（予定） | `C:\menkyo-sort` |
| `/chiken/` | 治験（予定） | `C:\chiken-sort` |

ルートの `sitemap.xml` は索引で、各ジャンルの sitemap を参照する。
ジャンルを足したら `migrate_to_sort.py` の `build_root_files()` とハブのカードを更新する。

## デプロイ

```
cd C:\resort-sort
python build_*.py          # 従来どおり public/ に出力（ルート直下前提のまま）
python migrate_to_sort.py  # public/ → C:\sort\resort\ へ組み替え
cd C:\sort
git add -A && git commit -m "..." && git push
```

ビルダー側は `/` 直下を前提に書かれたままで、`migrate_to_sort.py` が最後に
ルート相対リンク（約1,473本）と canonical / sitemap をまとめて `/resort/` 付きに直している。
各ビルダーに接頭辞を配るより、書き換え箇所が1か所で済むのでこうしている。

## 公開してはいけないもの

`public/` に置いたファイルは**そのままWebに出る**。
実際に `resort-sort.fumiproject.dev/STRATEGY.md`（社内向けの競合戦略メモ）が
HTTP 200 で誰でも読める状態になっていた。`migrate_to_sort.py` の `ROOT_ONLY` で
`STRATEGY.md` / `README.md` を公開対象から除外している。

GitHub Pages の無料枠は公開リポジトリでしか使えないため、**このリポジトリは public 必須**。
秘密情報・戦略メモ・鍵の類は絶対に置かない。

## 旧ドメイン

`resort-sort.fumiproject.dev` は削除せず転送専用にする（`fumi3333/resort-sort`）。
GitHub Pages は 301 を返せないので meta refresh + canonical + JS の3段。
生成は `C:\resort-sort\build_old_redirects.py`、作業コピーは `C:\resort-sort-old`。
