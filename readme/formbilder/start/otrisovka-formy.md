# Отрисовка формы

Для отрисовки полей используется [модель рендеринга из стора](podgotovka-stora.md) и компоненты групп и полей - [**FormBuilderGroup**](../formbuildergroup.md) и [**FormBuilderField**](../formbuilderfield.md).

### Получение формы из запроса

При получении ответа с объектом формы из запроса нужно передать ее в actions для модели рендеринга и модели данных:

```javascript
this.$apollo.query({
        query: GET_DEVELOPER_FORM,
        variables,
      }).then((res) => {
        let developerForm = [...res.data.developer_form.formElements] // забираем объект формы

        this.setRenderForm(developerForm)

        this.setDataModel(developerForm)

        this.formLoaded = true // отключаем лоадер
      }).catch(err => console.log(err))
```

### Отрисовка формы

Для отрисовки простой формы достаточно просто компонента FormBuilderGroup. Внутри себя он уже рендерит поля и вложенные группы за исключением групп с типом MAIN.

```html
<FormBuilderGroup
    v-for="group in getRenderForm"
    :key="group.order"
    :group="group"
    :changeField="inputField"
/>
```

Параметр changeField принимает функцию которая выполняется при изменении значения какого либо поля. Подробнее смотри в описании компонента [**FormBuilderGroup**](../formbuildergroup.md)
