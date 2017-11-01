<template>
  <div id="app">
    <section id="signInAndSignUp" v-if="!currentUser">
      <div>
        <label><input type="radio" name="type" v-model='actionType' value="signUp">注册</label>
        <label><input type="radio" name="type" v-model='actionType' value="login">登入</label>
      </div>
      <div class="signUp" v-show="actionType=='signUp'">
        <form v-on:submit.prevent='signUp'>
          <div class="formRow">
            用户名<input type="text" v-model='formData.username'>
          </div>
          <div class="formRow">
            密码<input type="password" v-model='formData.password'>
          </div>
          <div class="formActions">
            <input type="submit" value="注册">
          </div>
        </form>
      </div>
      <div class="login" v-show="actionType=='login'">
        <form v-on:submit.prevent="login">
          <div class="formRow">
            用户名<input type="text" v-model='formData.username'>
          </div>
          <div class="formRow">
            密码<input type="password" v-model='formData.password'>
          </div>
          <div class="formActions">
            <input type="submit" value="登入">
          </div>
        </form>
      </div>
    </section>

    <section id="todo" v-if="currentUser">
      <p><button v-on:click="logout">登出</button></p>
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
  import Vue from 'vue'
  import AV from 'leancloud-storage'

  var APP_ID = 'QxFzuBWaNf3z0YOWbyTAPM0X-gzGzoHsz';
  var APP_KEY = 'POPAqK6a4LlgOrIPtjKDBzBc';

  AV.init({
    appId: APP_ID,
    appKey: APP_KEY
  });



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
    this.currentUser = this.getCurrentUser();
  },
  data(){
      return {
        newTodo:'',
        todoList:[],
        actionType:'signUp',
        formData:{
            username:'',password:''
        },
        currentUser:''
      }
  },
  methods:{
    saveTodos: function(){
      let dataString = JSON.stringify(this.todoList)
        var AVTodos = AV.Object.extend('AllTodos');
      var avTodos = new AVTodos();
      var acl = new AV.ACL()
      acl.setReadAccess(AV.User.current(),true)
      acl.setWriteAccess(AV.User.current(),true)
      avTodos.set('content', dataString);
      avTodos.setACL(acl)
      avTodos.save().then(function (todo) {
          alert('保存成功');
        }, function (error) {
          alert('保存失败');
        });
    },
      addTodo(){
          this.todoList.push({
            title: this.newTodo,
            done: false
          })
        this.newTodo=''
        this.saveTodos()
      },
    removeTodo(index){
        this.todoList.splice(index,1)
      this.saveTodos()
    },
    signUp(){
      let user = new AV.User();
      user.setUsername(this.formData.username);
      user.setPassword(this.formData.password);
      user.signUp().then( (loginedUser)=> {
        this.currentUser=this.getCurrentUser()
      }, (error)=>{
          alert("注册失败")
      });
    },
    login(){
      AV.User.logIn(this.formData.username, this.formData.password).then( (loginedUser)=>{
        this.currentUser=this.getCurrentUser()
      }, (error)=>{
          alert('登录失败')
      });
    },
    getCurrentUser(){
      let current = AV.User.current()
         if (current) {
           let {id, createdAt, attributes: {username}} = current
           console.log({id, username, createdAt})
           return {id, username, createdAt}
         } else {
           return null
           }
    },
    logout(){
      AV.User.logOut()
        this.currentUser = null
        window.location.reload()
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
