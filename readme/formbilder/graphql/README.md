# GraphQL

В структуру самого запроса лучше не вдаваться и копировать как есть.&#x20;

Необходимые свойства полей добавлять во фрагмент fField, он подписан в [примере](../start/podgotovka-zaprosa.md) как фрагмент поля. Необходимые свойства групп добалять во фрагмент fGroup, он там же подписан как фрагмент группы.

### Общее

Корневой тип для FormBuilder - "FullStructureForm" и имеет в составе 2 основных и 1 вспомогательное поля для построения формы:

* **formGroups** - Список групп формы (тип _FormGroup_). Группы содержат списки полей. У групп также может быть родительская группа, таким образом строится иерархия полей/групп.
* **formFields** - Список полей формы (тип _FormField_), у которых нет родительской группы.
* **allFormFields** - Вспомогательное поле. Список всех полей формы (тип _FormField_), включая те, что находятся в группах.

На основе полей **formGroups** и **formFields** формируется форма с учётом задуманной структуры и порядком полей. Основу поведения/отрисовки поля или группы определяет их тип (поля **type** и **groupType** соответственно).

Пример стандартных фрагментов для обращения к этим полям

```graphql
fragment fField on FormField {
  name
  title
  value
  fieldDependencyList {
    additionalInfluencingFields
    dependencyType
    fieldNames
  }
  listOptions {
    name
    idField
    description
    value
  }
  groupName
  order
  hint
  inputType
  placeholder
  title
}

fragment fGroup on FormGroup {
  name
  groupType
  hint
  order
  parentGroupName
  suffix
  fields {
    ...fField
  }
}

```

Типы _FormGroup_ и _FormField_ имеют общий интерфейс _FormElementInterface_, что подразумевает их взаимодействие на одном уровне. Иными словами и группа и поле являются элементом формы и одинаково ведут себя при расположении: если у группы (в корне) стоит значение **order** равное 1, а у поля (в корне) значение **order** равное 0 (оба на одном уровне в корне), то значит сначала должно отрисоваться поле, а потом группа.
