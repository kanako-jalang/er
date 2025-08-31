```mermaid

erDiagram
  %% ========= Entities =========
  PASSAGE {
    string passage_id PK
    int    year
    string source        "出典（第○回など）"
    string title         "見出し（任意）"
    text   body          "本文（長文テキスト）"
    string difficulty    "日本語レベル目安（任意）"
  }

  ITEM {
    string item_id PK
    string passage_id FK "長文に紐づく（単問はNULL可）"
    int    year
    string category      "簡易ジャンル"
    text   stem          "設問本文"
    string correct_choice_key "A/B/C/D（任意。正誤はCHOICE側でも管理）"
  }

  CHOICE {
    string choice_id PK
    string item_id  FK
    string key          "A/B/C/D"
    text   text         "選択肢本文"
    boolean is_correct  "正解フラグ"
    text   rationale    "選択肢別の補足（任意）"
  }

  EXPLANATION {
    string explanation_id PK
    string item_id        FK
    text   text_ja_simple "やさしい日本語の解説"
    text   glossary_terms "用語リスト（;区切り等）"
  }

  TAG {
    string tag_id PK
    string name
  }

  ITEM_TAG {
    string item_tag_id PK
    string item_id  FK
    string tag_id   FK
  }

  ASSET {
    string asset_id PK
    string url
    string kind   "image/audio/etc."
    string alt    "代替テキスト"
  }

  PASSAGE_ASSET {
    string id PK
    string passage_id FK
    string asset_id   FK
  }

  ITEM_ASSET {
    string id PK
    string item_id  FK
    string asset_id FK
  }

  CHOICE_ASSET {
    string id PK
    string choice_id FK
    string asset_id  FK
  }

  %% ========= Relationships =========
  PASSAGE ||--o{ ITEM           : contains
  ITEM    ||--o{ CHOICE         : offers
  ITEM    ||--o{ EXPLANATION    : explains
  ITEM    ||--o{ ITEM_TAG       : labeled_by
  TAG     ||--o{ ITEM_TAG       : tags

  PASSAGE ||--o{ PASSAGE_ASSET  : has_asset
  ASSET   ||--o{ PASSAGE_ASSET  : used_in

  ITEM    ||--o{ ITEM_ASSET     : has_asset
  ASSET   ||--o{ ITEM_ASSET     : used_in

  CHOICE  ||--o{ CHOICE_ASSET   : has_asset
  ASSET   ||--o{ CHOICE_ASSET   : used_in
