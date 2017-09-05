**computed**
-   只有当`依赖`的属性被加入渲染列表中，且`被依赖`的属性发生变化时，才会调用计算属性里面的相应方法。

```JavaScript
    //dom 部分
    <div id="computed-example">
        <p>
            Ask a yes/no question:
            <input type="text" v-model="question">
        </p>
        <p>{{ answer }}</p>
        //当我们把这行删除之后，对应的 `questionComputed` 方法将不会调用
        <p style="display: none">{{ questionComputed }}</p>
    </div>


    //js部分
    var computedExampleVM = new Vue({
            el: '#computed-example',
            data: {
                question: '',
                answer: 'I cannot give you an answer until you ask a question!'
            },
            computed: {
                questionComputed: function () {
                    this.answer = 'Waiting for you to stop typing...'
                    this.getAnswer()
                    return this.question;
                }
            },
            methods: {
                getAnswer: _.debounce(
                    function () {
                        if (this.question.indexOf('?') === -1) {
                            this.answer = 'Questions Questions usually contain a question mark. ;-)'
                            return
                        }
                        this.answer = 'Thinking...'
                        var vm = this
                        axios.get('https://yesno.wtf/api')
                            .then(function (response) {
                                vm.answer = _.capitalize(response.data.answer)
                            })
                            .catch(function (err) {
                                vm.answer = 'Error! Could not reach the API. ' + err
                            })
                    },
                    500
                )
            }
        })
```

- 而对应的`watch`就只需要对应的`data`属性变化就行，并不需要加入渲染列表

总结，在需要进行一步操作的时候最好使用`watch`属性
