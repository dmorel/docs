### Typical JS code

```javascript
var app = new Vue({
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      users: [
        {firstname: 'Sebastian', lastname: 'Eschweiler'},
        {firstname: 'Bill', lastname: 'Smith'},
        {firstname: 'John', lastname: 'Porter'}
      ],
      input_val: '',
      counter: 0
    }
  },
  methods() {
    return []
      addUser() {
        return this.users.push(this.newUser)
      },
      …
    ]
  }
})
```

### Binding an attribute

```
<div id="app">
  {{ message }}
</div>

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

```
<span v-bind:title="message">

var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date()
  }
})
```

### shortcuts

- v-bind is shortened to ':' so `<img v-bind:title="fooBar">` becomes `<img :title="fooBar">`
- v-on is shortened to '@' so `<button v-on:click="incrementThis">` becomes `<button @click="incrementThis">`




https://laracasts.com/series/learn-vue-2-step-by-step/episodes/1



