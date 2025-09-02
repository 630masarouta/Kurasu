# テーブル設計

## users テーブル

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| name            | string      | null: false                    |
| email           | string      | null: false, unique: true      |
| password_digest | string      | null: false                    |

### Association
has_many :menus, dependent: :destroy
has_many :shopping_lists, dependent: :destroy
has_many :stocks, dependent: :destroy
has_many :dishes, dependent: :destroy


## items テーブル (新規)
食材や物品のマスターデータ（辞書）。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| name            | string      | null: false, unique: true      |
| category        | string      | null: false                    |

### Association
has_many :stocks, dependent: :destroy
has_many :dish_ingredients, dependent: :destroy
has_many :shopping_list_items, dependent: :destroy


## menus テーブル
特定の日の献立（朝食、昼食、夕食など）を管理する。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| user_id         | big integer | null: false, foreign_key: true |
| name            | string      | null: false, default: '献立'   |
| date            | date        | null: false                    |

### Association
belongs_to :user
has_many :menu_dishes, dependent: :destroy
has_many :dishes, through: :menu_dishes


## dishes テーブル (新規)
ユーザーが作成する料理（レシピ）を管理する。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| user_id         | big integer | null: false, foreign_key: true |
| name            | string      | null: false                    |
| instructions    | text        |                                |

### Association
belongs_to :user
has_many :dish_ingredients, dependent: :destroy
has_many :items, through: :dish_ingredients
has_many :menu_dishes, dependent: :destroy
has_many :menus, through: :menu_dishes


## dish_ingredients テーブル (新規)
料理（dish）とそれに必要な食材（item）を紐付ける中間テーブル。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| dish_id         | big integer | null: false, foreign_key: true |
| item_id         | big integer | null: false, foreign_key: true |
| quantity        | decimal     | null: false                    |
| unit            | string      | null: false                    |

### Association
belongs_to :dish
belongs_to :item
*Note: `dish_id` と `item_id` の組み合わせはユニークであるべき。*


## menu_dishes テーブル (新規)
献立（menu）と料理（dish）を紐付ける中間テーブル。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| menu_id         | big integer | null: false, foreign_key: true |
| dish_id         | big integer | null: false, foreign_key: true |

### Association
belongs_to :menu
belongs_to :dish
*Note: `menu_id` と `dish_id` の組み合わせはユニークであるべき。*


## shopping_lists テーブル 

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| user_id         | big integer | null: false, foreign_key: true |
| name            | string      | null: false                    |
| created_at      | datetime    | null: false                    |

### Association
belongs_to :user
has_many :shopping_list_items, dependent: :destroy
has_many :items, through: :shopping_list_items


## stocks テーブル 
ユーザーの在庫を管理する。

| Column          | Type        | Options                        |
| --------------- | ----------- | ------------------------------ |
| id              | big integer | null: false, primary key       |
| user_id         | big integer | null: false, foreign_key: true |
| item_id         | big integer | null: false, foreign_key: true |
| quantity        | decimal     | null: false                    |
| unit            | string      |                                |
| expiration_date | date        |                                |

### Association
belongs_to :user
belongs_to :item
*Note: `user_id` と `item_id` の組み合わせはユニークであるべき。*


## shopping_list_items テーブル 
買い物リストの各品目を管理する。

| Column           | Type        | Options                        |
| ---------------- | ----------- | ------------------------------ |
| id               | big integer | null: false, primary key       |
| shopping_list_id | big integer | null: false, foreign_key: true |
| item_id          | big integer | null: false, foreign_key: true |
| quantity         | integer     | null: false                    |
| unit             | string      |                                |
| is_purchased     | boolean     | null: false, default: false    |

### Association
belongs_to :shopping_list
belongs_to :item
*Note: `shopping_list_id` と `item_id` の組み合わせはユニークであるべき。*