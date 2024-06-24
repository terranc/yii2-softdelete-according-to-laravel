# yii2-softdelete-according-to-laravel

```php
class WtTest extends ActiveRecordBase
{
    /**
     * @inheritdoc
     */
    public static function tableName()
    {
        return 'wt_test';
    }

    public function behaviors()
    {
        return [
            [
                'class' => TimestampBehavior::class,
                'value' => function() {
                    return date('Y-m-d H:i:s');
                },
            ],
            [
                'class' => SoftDeleteBehavior::class,
                'softDeleteAttributeValues' => [
                    'deleted_at' => function() {
                        return date('Y-m-d H:i:s');
                    }
                ],
                'replaceRegularDelete' => true,
                'restoreAttributeValues' => [
                    'deleted_at' => null,
                ],
            ],
        ];
    }

    public static function find()
    {
        return new WtTestQuery(get_called_class());
    }

    public function delete()
    {
        return $this->softDelete();
    }
}

```

```php
class WtTestQuery extends ActiveQuery
{
    public function init()
    {
        parent::init();
        $this->andWhere(['deleted_at' => null]);
    }

    public function withTrashed()
    {
        $this->removeSoftDeleteCondition();
        return $this;
    }

    public function onlyTrashed()
    {
        $this->removeSoftDeleteCondition();
        return $this->andWhere(['not', ['deleted_at' => null]]);
    }

    private function removeSoftDeleteCondition() {
        foreach ($this->where as $key => $condition) {
            if (
                $key === 'deleted_at'
                || (is_array($condition) && $condition[0] === 'deleted_at')
            ) {
                unset($this->where[$key]);
            }
        }
    }
}
```
