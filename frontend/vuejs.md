# VueJS

## Ambiente
* Instalar CLI: `npm install -g vue-cli`

* Criar projeto: `vue init webpack-simple <project_name>`


## Vue
Reponsável por vincular a view e o componente.
Sempre iniciamos o app instanciando o Vue e passando um objeto com opções.

Algumas opções disponíveis:
* el: Seletor do elemento do HTML em que será renderizado o componente. Se não for informado um então será chamado a função em `vm.$mount(el)`.
* render: Função que define qual componente será renderizado.

Quando a instância Vue é criada, todos os atributos do objeto passado no atributo `data` do objeto de opções são adicionado ao sistema reativo, logo se um deles sofrer uma alteração então a View será refletida.
Porém, novos atributos que forem adicionados nesse objeto não será reflatido.
Os atributos de `data` são 'setados' na instância de View.

```
const myData = {
	message: 'Atributo do model'
};
const vm = new Vue({
  data: myData
})

// Algumas propriedades de Vue
vm.$data === myData; // true
vm.$el = document.getElementById('divToRender');
vm.$watch('message', function(newValue, oldValue) {
  // callback de mudanças no atributo message
  // ao usar arrow function perdemos referencia ao Vue (this da função não será a mesma)
  
});
```
* `vm.message = 'Novo valor do atributo'`: atualiza a view model
* `vm.anotherMessage = 'Não atualiza nada'`: não gera nenhum evento


### Computed Property
São funções que são invocados como getter.
Vue consegue identificar quais propriedades a propriedade computada depende, então ela somente é evaluda quando uma de suas propriedades dependentes for atualizada, até seu resultado é cacheado.
```
new Vue({
  data: { message: 'Atributo do model' },
  computed: {
    reversedMessage: function() {
      return this.message.split('').reverse().join('');
    }
  }
})
```

### Watched Property
Mais verboso e repetitivo que o computed property.
```
new Vue({
  data: { message: 'Atributo do model', reversedMessage: null },
  watch: {
    message: function(newMessage) {
      this.reversedMessage = newMessage.split('').reverse().join('');
    }
  }
})
```


### Lifecycle Hooks
Cuidado ao usar arrow function pois o `this` não apontará para a instância de Vue.
Fluxo do ciclo de vida: [diagrama](https://vuejs.org/images/lifecycle.png?_sw-precache=6f2c97f045ba988851b02056c01c8d62)

* beforeCreate
* created
invocado após a criação da instãncia.
```
new Vue({
  data: { value: 1 },
  created: function() {
    console.log('Model: ', this.value);
  }
});
```
* beforeMount
* mounted
* beforeUpdate
* updated
* beforeDestroy
* destroyed

  
## Template
Podemos renderizar dados de forma declarativa usando templates HTML.
Podemos usar interpolação ou linkar atributos do elemento com diretiva do Vue (tem prefixo `v-`).
Vue compila o template em funções de renderização do Virtual DOM (mas também podemos escrever nossas funções diretamente ou utilizar JSX).

```
<div id="app" v-bind:title="title">
  {{ message }}
</div>
```
```
var app = new Vue({
  el: '#app',
  data: {
    title: 'Message',
    message: 'Hello Vue!',
    onClickHandler: () => console.log('Clicked')
  }
});
app.message = 'Reactive data =)'
```


## Interpolações e Diretivas
Tem prefixo `v-` para evitar problemas com a linguagem do template.
Algumas diretivas recebem argumentos que são definidos após o `:`.
Algumas diretivas recebem modificarores que são definidos após o `.`.

As diretivas que `v-bind` e `v-on` possuem shorthands para evitar verbosidade, quando desenvolvemos um SPA onde Vue cuida da renderização do template não há problemas em usá-las.

* {{ }}

* v-bind
```
<div v-bind:title="titleVar"></div>
<label :for="inputFieldName"></label>
<button v-bind:disabled="isDisabled">Click</button>
```
* v-html
```
<div v-html="richText"></div>
```

* v-on
```
<button v-on:click="onClickHandler">Click 1</button>
<button @click="onClickHandler">Click 2</button>
<form v-on:submit.prevent="onSubmit"></form>
```

* v-if
```
<div v-if="showDivVar"></div>
```

* v-for
```
<ul>
  <li v-for="todo in todos">{{ todo.text }}</li>
</ul>
```

* v-model
```
<input type="text" v-model="message">
```


## Componentes
É essencialmente uma instância Vue com opções pré-definidas.



## Links
* [Playground](https://jsfiddle.net/chrisvfritz/50wL7mdz/)
