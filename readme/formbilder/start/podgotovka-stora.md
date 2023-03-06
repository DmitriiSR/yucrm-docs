# Подготовка стора

Логика формбилдера разделена на две ключевые составляющие: работа с данными и отрисовка.&#x20;

Например:\
Если нужно перерисовать какое то поле, то необходимо будет заменить объект поля в модели рендеринга. Т.e. работаем с моделью рендеринга

Если нужно изменить значение какого-то поля, то необходимо менять это поле в модели данных. Т.e. работаем с моделью данных.

Для работы с формой необходимо создать в сторе модели для рендеринга и данных. Для всех моделей необходимы геттеры и экшены.&#x20;

```javascript
export const Store = {
    state: () => ({
        dataModel: {},
        renderForm: [],
    }),

    getters: {
       getRenderForm(state) {
           return state.renderForm
       },
       getDataForRequest(state) {
          ... 
       }
    },

    mutations: {
        setDataModel(state, form) {
            ... 
        },
        setRenderForm(state, form) {
           ... 
        }
    },

    actions: {
        setDataModel({commit}, form) {
            commit('setDataModel', form)
        },
        setRenderForm({commit}, form) {
            commit('setRenderForm', form)
        }
    }
}
```

Названия и реализация моделей и функций могут быть любыми, главное чтобы была соблюдена логика.

### Модель для данных

Модель для данных нужна для хранения значения в полях. Ее геттер возвращает готовый к отправке на бэк объект.&#x20;

Функция **setDataModel** необходима чтобы рекурсивно развернуть полученный с бэка объект. Функция возвращает объект, в  котором имя свойства это имя поля, а значение это объект самого поля.\
В итоге должен получиться объект вида:

```
{
    name: {
        name:"name",
        title:"Название",
        value:"тест соответствий",
        fieldDependencyList: null,
        ...
    },
    housing_class: {
        name:"housing_class",
        title:"Класс жилья",
        value: 2,
        fieldDependencyList: null,
        ...
    },
    ...
}
```

<details>

<summary>Пример мутации <strong>setDataModel</strong></summary>

```javascript
setDataModel(state, form) { // На вход получает объект формы с бэка
            let dataModel = {};

            function setShowToFields(model) { // Рекурсивная функция которая создает свойство по названию поля и кладет внего объект самого поля
                model.forEach(element => {
                    if (!element.elements) {
                        dataModel[element.name] = JSON.parse(JSON.stringify(element))
                    } else {
                        setShowToFields(element.elements)
                    }

                })
            }

            setShowToFields(form)
            state.dataModel = {...dataModel};
        },
```

</details>

Геттер модели для данных - **getDataForRequest** возвращает готовый к отправке на бэк объект вида {"название поля": "значение  поля"}. Этот геттер проверяет какой тип данных принимает поле и подстраивает тип под необходимый. В примере этот геттер называется

<details>

<summary>Пример геттера <strong>getDataForRequest</strong></summary>

```javascript
getDataForRequest(state) {
           let requestData = {}; // создаем объект который вернет геттер
           let formModel = state.dataModel; // переменная хранит состояние модели данных

           for (let key in formModel) { // проходим по каждому полю в модели
               if (formModel[key].value === null) break // если значения поля null то это поле не нужно отправлять на бэк

               if (formModel[key].type === 'SELECT' && formModel[key].value === '') break // то же самое если это селект который хранит пустую строку в качестве значения

               if (formModel[key].inputType === 'Int') { // проверяем какой тип должен быть у поля и добавляем в объект свойство со значением нужного типа
                   requestData[key] = Number(formModel[key].value)
               } else if (formModel[key].inputType === 'String') {
                   requestData[key] = String(formModel[key].value)
               }
               else if (formModel[key].inputType === 'Boolean') {
                   requestData[key] = Boolean(formModel[key].value)
               }
           }

           return requestData // возвращаем получившийся объект
       }
```

</details>

### Модель для рендеринга

Модель для рендеринга используется для отрисовки полей и групп. Изначально это сам ответ с бэка. Ее геттер возвращает эту модель. Мутация **setRenderForm** рекурсивно проходит по полученному с бэка объекту и добавляет каждому элементу обязательное свойство **show**, которое используется для отображения/скрытия полей. Свойство **show** необходимо добавлять в любом случае, даже если в форме не предусмотрено скрытие или отображение полей.

<details>

<summary>Пример мутации <strong>setRenderForm</strong></summary>

```javascript
setRenderForm(state, form) { // функция принимает полученный с бэка объект формы
            function setShowToFields(model) { // рекурсивная функция которая добавляет каждому элементу свойство show
                model.forEach(element => {
                    if (!element.elements) {
                        element.show = true;
                    } else {
                        setShowToFields(element.elements)
                    }

                })
            }

            setShowToFields(form)

            state.renderForm = JSON.parse(JSON.stringify(form)) // добавляем новую форму для рендеринга в состояние стора
        }
```

</details>
