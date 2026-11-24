# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：glossary_search_glossary
- STEP 2 の [tool call]：`glossary_search_glossary({'query': '3レベル'})`
- STEP 3 の [tool call]：
  - `fetch_fetch({'url': 'https://modelcontextprotocol.io/'})` → タイムアウト（30 秒）
  - `fetch_fetch({'url': 'https://modelcontextprotocol.io/introduction'})` → 成功
- STEP 4 の [tool call]：
  - `aws_aws___search_documentation({'search_phrase': 'Amazon S3 バージョニング とは', 'topics': ['general']})`
  - `aws_aws___search_documentation({'topics': ['general'], 'search_phrase': 'Using versioning in S3 buckets'})`
- 気づき・疑問：
  - ツールの説明文は、作者が書いたものがそのままモデルに渡る。
    `fetch_fetch` には「もうネットにつながるので、そう伝えて」というモデルへの指示まで入っていた。
    外部サーバーを足す前に `--list-tools` で説明文を読む必要がある。
  - STEP 3 では 1 回目の取得が失敗しても、モデルが別の URL を自分で選んでやり直した（モデル応答 3 回）。
  - STEP 4 では、日本語で検索したあと英語で検索し直していた。
  - `gemini-3.5-flash-lite` の 503（混雑）がときどき出た。ツールを使うとモデルを何度も呼ぶので、失敗しやすい。

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎
- やったこと：`app/mcp_server.py` に `server.tool()(tools.get_lecture_summary)` の 1 行を足した。エージェント側（`mcp_servers.json`・`mcp_agent.py`）はこの宿題のために変えていない。
- 結果：
  - `--list-tools` に `glossary_get_lecture_summary` が増えた
  - 「第2回では何を学びますか？」で `glossary_get_lecture_summary({'lecture': 2})` が呼ばれ、第2回の要点（ファインチューニングと LoRA）で答えた
- 気づき・疑問：
  - エージェントは起動のたびにサーバーへツールの一覧を問い合わせる。なので、サーバーに足すだけで使えるようになる。
  - 逆に言うと、サーバー側が変われば、モデルに渡るツールもこちらが知らないうちに変わる。外部サーバーの版を固定する理由が分かった。
