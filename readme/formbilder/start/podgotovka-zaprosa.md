# Подготовка запроса

Описание структуры GraphQL запроса, групп и полей смотри [здесь](../graphql/)

Для получения формы необходимо слать рекурсивный запрос.

```graphql
fragment fField on FormField { // Фрагмент поля формы
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
  type
  inputType
  placeholder
  title
  required
}

fragment fGroup on FormGroup { // Фрагмент группы полей
  groupType
  hint
  order
  parentGroupName
  suffix
}

query( $developerId: ID ){ // Основной запрос, предполагается вложенность до 7 уровня
  developer_form(developerId: $developerId) {
    formElements {
      name
      ... on FormField {
        ...fField
      }
      ... on FormGroup {
        ...fGroup
        elements {
          name
          ... on FormField {
            ...fField
          }
          ... on FormGroup {
            ...fGroup
            elements {
              name
              ... on FormField {
                ...fField
              }
              ... on FormGroup {
                ...fGroup
                elements {
                  name
                  ... on FormField {
                    ...fField
                  }
                }
              }
            }
          }
        }
      }
      order
    }
  }
}
```

Рекурсивная структура необходима для более простой отрисовки. Например, чтобы мы могли получить группу внутри которой поля и еще одна вложенная группа, и в таком же порядке их рендерить.
