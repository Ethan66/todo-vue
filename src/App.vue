<template>
  <div id="app">
    <section id="signInAndSignUp">
      <div>
        <label><input type="radio" name="type" value="signUp">注册</label>
        <label><input type="radio" name="type" value="login">登入</label>
      </div>
      <div class="signUp">
        <form>
          <div class="formRow">
            用户名<input type="text">
          </div>
          <div class="formRow">
            密码<input type="password">
          </div>
          <div class="formActions">
            <input type="submit" value="注册">
          </div>
        </form>
      </div>
      <div class="login">
        <form>
          <div class="formRow">
            用户名<input type="text">
          </div>
          <div class="formRow">
            密码<input type="password">
          </div>
          <div class="formActions">
            <input type="submit" value="登入">
          </div>
        </form>
      </div>
    </section>

    <section id="todo">
      <div class="newTask">
        <input type="text" v-model='newTodo' v-on:keyup.enter="addTodo">
      </div>
      <ol class="todos">
        <li v-for='(todo,index) in todoList'>
          <input type="checkbox" v-model='todo.done' />{{todo.title}}
          <span v-if="todo.done">已完成</span>
          <span v-else>未完成</span>
          <button v-on:click='removeTodo(index)'>X</button>
        </li>
      </ol>
    </section>
  </div>
</template>

<script>


export default {
  name: 'app',
  components: {

  },
  created(){
    window.onbeforeunload = ()=>{
      let dataString = JSON.stringify(this.todoList)
      window.localStorage.setItem('myTodos', dataString)
    }

    let oldDataString = window.localStorage.getItem('myTodos')
    let oldData = JSON.parse(oldDataString)
    this.todoList = oldData || []
  },
  data(){
      return {
          newTodo:'',
          todoList:[]
      }
  },
  methods:{
      addTodo(){
          this.todoList.push({
            title: this.newTodo,
            done: false
          })
        this.newTodo=''
      },
    removeTodo(index){
        this.todoList.splice(index,1)
    }
  }
}
</script>

<style lang="scss">
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    min-height: 100vh;
  }
  .icon {
    width: 1em; height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }


</style>
