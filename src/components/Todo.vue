<template>
  <div class="todoapp">
    <!-- head -->
    <div class="head" id="33">
      <h1>todos</h1>
      <input
        type="text"
        class="new-todo"
        v-model="inputText"
        placeholder="请输入文本"
        @keyup.enter="addTodo"
        autofocus
      />
    </div>
    <!-- end -->
    <!-- main -->
    <div class="main">
      <input type="checkbox" class="allselect-checkbox" v-model="isAllSelect" />
      <ul class="todo-list">
        <li
          v-for="(item, index) in filteredTodos"
          :class="{complected:item.isComplected,editing:item == editedTodo}"
          :key="index"
          class="item"
        >
          <div class="view">
            <input type="checkbox" v-model="item.isComplected" />
            <label @dblclick="editTodo(item)">{{ item.text }}</label>
            <button class="delete-btn" @click="removeTodo(item)">X</button>
          </div>
          <input
            type="text"
            class="edit"
            @keyup.enter="doneEdit(item)"
            @keyup.esc="cancelEdit(item)"
            @blur="doneEdit(item)"
            autofocus
            v-model="item.text"
          />
        </li>
      </ul>
    </div>
    <!-- end -->
    <!-- footer -->
    <div class="footer" v-if="todos.length">
      <span class="todo-count"
        >{{ remaining }} {{ remaining | pluralize }} left <a href="#/dd"></a>
      </span>
      <ul class="filters" @click=onClick($event)>
        <li>
          <a href="#/all"  :class='{selected:visibility=="all"}' data-value="All">All</a
          >
        </li>
        <li>
          <a
            href="#/active"
            :class='{selected:visibility=="active"}'
            data-value="Active"
            >Active</a
          >
        </li>
        <li>
          <a
            href="#/completed"
            :class='{selected:visibility=="complected"}'
            data-value="Complected"
            >Complected</a
          >
        </li>
      </ul>
          <div class="wrapper"> <button class="clear-complected" @click='removeCompleted' v-if="todos.length>remaining">clear complected</button>
          </div>
    </div>
    <!-- end -->
  </div>
</template>

<script>
  const filters = {
    complected(todos) {
      return todos.filter(todo => todo.isComplected);
    },
    active(todos) {
      return todos.filter(todo => !todo.isComplected);
    },
    all(todos) {
      return todos;
    }
  };
const todoStorage={
    fetch:function(){
        const todos=JSON.parse(localStorage.getItem('todos')||'[]')
        todos.forEach((todo,index)=>todo.id=index)
        todoStorage.uid=todos.length
        console.log(111);
        
        return todos
    },
    save:function(todos){
        localStorage.setItem('todos',JSON.stringify(todos))
        console.log(2222);
        
    }
}
  export default {
    data() {
      return {
        inputText: "",
        todos:todoStorage.fetch(),
        originId: 0,
        visibility: "all",
        editedTodo: null
      };
    },
    watch: {
        todos:{
            aa:function(newVal,oldVal){
                todoStorage.save(newVal)                
            },
            deep:true
        }
    },
    computed: {
      filteredTodos: function() {
        return filters[this.visibility](this.todos);
      },
      remaining: function() {
        return filters.active(this.todos).length;
      },
      isAllSelect: {
        get: function() {
          return this.remaining === 0;
        },
        set: function(value) {
          this.todos.forEach(item => (item.isComplected = value));
        }
      }
    },
    
    filters: {
      pluralize: function(n) {
        return n > 1 ? "items" : "item";
      }
    },
    directives: {
      "todo-focus": function(el, binding) {
        if (binding.value) {
          el.focus;
        }
      },
      focus (el, { value }, { context }) {
      if (value) {
        context.$nextTick(() => {
          el.focus()
        })
      }
    }
    },
    methods: {
      onClick(e) {
          let value=e.target.text.toLowerCase();
        this.visibility=value
      },
       removeCompleted(){
           this.todos=filters.active(this.todos)
       },
      cancelEdit(item) {
        this.editedTodo = null;
      },
      doneEdit(item) {
        this.editedTodo = null;
        item.text = item.text.trim();
        if (!item.text) {
          this.removeTodo(item);
        }
      },
      editTodo(todo) {
        this.editedTodo = todo;
      },
      removeTodo(todo) {
        this.todos.splice(this.todos.indexOf(todo), 1);
      },
      addTodo() {
        let value = this.inputText.trim();

        if (!value) return;
        this.todos.push({
          id: this.originId++,
          text: value,
          isComplected: false
        });
        this.inputText = "";
      }
    }
  };
</script>

<style>
  html,
  body,
  * {
    margin: 0;
    padding: 0;
  }
  .clear-complected{
      position: relative;
      z-index: 10;
  }
  button {
    margin: 0;
    padding: 0;
    border: 0;
    background: none;
    font-size: 100%;
    vertical-align: baseline;
    font-family: inherit;
    font-weight: inherit;
    color: inherit;
    -webkit-appearance: none;
    appearance: none;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  body {
    font: 14px "Helvetica Neue", Helvetica, Arial, sans-serif;
    line-height: 1.4em;
    background: #f5f5f5;
    color: #4d4d4d;
    min-width: 230px;
    max-width: 550px;
    margin: 0 auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    font-weight: 300;
  }

  button,
  input[type="checkbox"] {
    outline: none;
  }

  .hidden {
    display: none;
  }
  ul,
  li {
    list-style: none;
  }
  .todoapp {
    background: #fff;
    margin: 130px 0 40px 0;
    position: relative;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
  }

  .todoapp input::-webkit-input-placeholder {
    font-style: italic;
    font-weight: 300;
    color: #e6e6e6;
  }

  .todoapp input::-moz-placeholder {
    font-style: italic;
    font-weight: 300;
    color: #e6e6e6;
  }

  .todoapp input::input-placeholder {
    font-style: italic;
    font-weight: 300;
    color: #e6e6e6;
  }

  .todoapp h1 {
    position: absolute;
    top: -65px;
    width: 100%;
    font-size: 100px;
    font-weight: 100;
    text-align: center;
    color: rgba(175, 47, 47, 0.15);
    -webkit-text-rendering: optimizeLegibility;
    -moz-text-rendering: optimizeLegibility;
    text-rendering: optimizeLegibility;
  }
  .new-todo,
  .edit {
    position: relative;
    margin: 0;
    width: 100%;
    font-size: 24px;
    line-height: 1.4em;
    border: 0;
    outline: none;
    padding: 6px;
    border: 1px solid #999;
    box-shadow: inset 0 -1px 5px rgba(0, 0, 0, 0.2);
    box-sizing: border-box;
  }

  .new-todo {
    padding: 16px 16px 16px 60px;
    border: none;
    background: rgba(0, 0, 0, 0.003);
    box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
  }
  .main {
    z-index: 2;
    border-top: 1px solid #e6e6e6;
    overflow: hidden;
  }
  .allselect-checkbox {
    position: absolute;
    top: 11px;
    left: 10px;
    width: 60px;
    height: 34px;
    text-align: center;
    border: none;
    appearance: none;
    transform: rotate(90deg);
  }
  .allselect-checkbox::before {
    content: "❯";
    font-size: 22px;
    color: #e6e6e6;
    padding: 10px 27px;
  }
  .allselect-checkbox:checked::before {
    color: red;
  }
  .todo-list li {
    border-bottom: 1px solid #ededed;
    position: relative;
    font-size: 24px;
  }
  .todo-list li:last-child {
    border-bottom: none;
  }
  .todo-list > li.complected label {
    color: #d9d9d9;
    text-decoration: line-through;
  }
  .todo-list li.editing .edit {
    display: block;
    width: 506px;
  }
  .todo-list li.editing .view {
    display: none;
  }
  .edit {
    display: none;
    margin-left: 50px;
    padding-left: 20px;
    border: 1px solid rgba(173, 207, 241, 0.2);
  }
  .view {
    display: flex;
    justify-content: space-between;
    margin: 0 10px;
    text-align: left;
    align-items: center;
    height: 60px;
  }
  .view .delete-btn {
    visibility: hidden;
    color: red;
    flex: 1;
  }
  .view:hover > .delete-btn {
    visibility: initial;
  }
  .view > input {
    flex: 1;
    width: 16px;
    height: 16px;
  }
  .view > label {
    line-height: 50px;
    flex: 8;
  }
  .footer {
    color: #777;
    padding: 10px 15px;
    height: 20px;
    text-align: center;
    border-top: 1px solid #e6e6e6;
    display: flex;
    justify-content: space-between;
  }
  .footer::before {
    content: "";
    position: absolute;
    right: 0;
    left: 0;
    bottom: 0;
    height: 50px;
    overflow: hidden;
    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6,
      0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6,
      0 17px 2px -6px rgba(0, 0, 0, 0.2);
  }
  .filters {
    display: flex;
    z-index: 10;
  }
  .wrapper{
      width: 150px;
  }
  .filters li a {
    margin: 0 5px;
    padding: 0 8px;
    border: 1px solid transparent;
    border-radius: 2px;
  }
  .filters a:hover,
  .filters a.selected {
    border-color: red;
  }
  .filters a {
    text-decoration: none;
  }
</style>
