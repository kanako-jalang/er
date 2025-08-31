```mermaid


erDiagram
  %% ===== 既存（抜粋） =====
  PASSAGE {
    string passage_id PK
    int    year
    string source
    string title
    text   body
    string difficulty
  }
  ITEM {
    string item_id PK
    string passage_id FK "単問はNULL可"
    int    year
    string category
    text   stem
  }
  CHOICE {
    string choice_id PK
    string item_id  FK
    string key
    text   text
    boolean is_correct
    text   rationale
  }
  EXPLANATION {
    string explanation_id PK
    string item_id        FK
    text   text_ja_simple
    text   glossary_terms
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
    string kind
    string alt
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

  %% ===== 新規（出題・ログ・復習） =====
  COHORT {
    string cohort_id PK "クラス/期/コース"
    string name
  }

  SESSION {
    string session_id PK
    string cohort_id  FK "任意：クラス単位の回なら"
    date   held_on
    int    target_items "出題数(15-30)"
    string mode         "random/adaptive"
    string notes
  }

  SESSION_ITEM {
    string session_item_id PK
    string session_id  FK
    string item_id     FK
    int    order_index
  }

  USER {
    string user_id PK  "任意(匿名IDでも可)"
    string display_name
  }

  RESPONSE {
    string response_id PK
    string session_id  FK
    string session_item_id FK
    string item_id     FK
    string user_id     FK "任意"
    string selected_key   "A/B/C/D"
    boolean is_correct
    string confidence     "low/med/high"
    int    response_sec
    datetime answered_at
  }

  WRONG_SUMMARY {
    string wrong_id PK
    string cohort_id  FK "クラス別にまとめ"
    string item_id    FK
    int    wrong_count
    int    unsure_count
    datetime last_seen_at
  }

  ITEM_STATS {
    string item_id PK
    int    shown_count
    int    correct_count
    int    wrong_count
    int    unsure_count
    float  avg_time_sec
    float  difficulty_score "自動算出用(例:1.0-5.0)"
    datetime last_seen_at
  }

  %% ===== リレーション =====
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

  COHORT  ||--o{ SESSION        : holds
  SESSION ||--o{ SESSION_ITEM   : selects
  ITEM    ||--o{ SESSION_ITEM   : picked_as

  SESSION ||--o{ RESPONSE       : records
  USER    ||--o{ RESPONSE       : answers
  ITEM    ||--o{ RESPONSE       : answered

  COHORT  ||--o{ WRONG_SUMMARY  : accumulates
  ITEM    ||--o{ WRONG_SUMMARY  : counted_as

  ITEM    ||--|| ITEM_STATS     : aggregates
