<template>
    <div>
        <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">
        <todo-item v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index" :checkAll="!anyRemaining">
         
        </todo-item>
        <div class="extra-container">
            <todo-check-all :anyRemaining="anyRemaining"></todo-check-all>
            <todo-item-remaining :remaining="remaining"></todo-item-remaining>
        </div>
        <div class="extra-container">
            <todo-filtered></todo-filtered>
            <div>
                <todo-clear-completed :showClearCompletedButton="showClearCompletedButton"></todo-clear-completed>
            </div>
        </div>
    </div>
</template>
<script>
import TodoItem from './TodoItem'
import TodoItemRemaining from './TodoItemRemaining'
import TodoCheckAll from './TodoCheckAll'
import TodoFiltered from './TodoFiltered'
import TodoClearCompleted from './TodoClearCompleted'
import {eventBus} from ''
export default {
    name:'todo-list',
    components: {
        TodoItem,
        TodoItemRemaining,
        TodoCheckAll,
        TodoFiltered,
        TodoClearCompleted
    },
    data (){
        return {
            newTodo : '',
            idForTodo: 3,
            beforeEditCache : '',
            todos : [
                {
                    'id' : 1,
                    'title' :'Finish Vue Screencast',
                    'completed' :false,
                    'editing': false
                },
                 {
                    'id' : 2,
                    'title' :'Take over world',
                    'completed' :false,
                    'editing':false
                }
            ]
        }
    },
    created(){
        eventBus.$on('removedTodo', (index) => this.removeTodo(index))
        eventBus.$on('finishedEdit', (data) =>this.finishedEdit(data))
        eventBus.$on('checkAllChanged', (checked) =>this.checkAllTodos(checked))
        eventBus.$on('filterChanged', (filter) =>this.filter=filter)
        eventBus.$on('clearCompletedTodos', () =>this.clearCompleted())
    },
    beforeDestroy(){
        eventBus.$off('removedTodo', (index) => this.removeTodo(index))
        eventBus.$off('finishedEdit', (data) =>this.finishedEdit(data))
        eventBus.$off('checkAllChanged', (checked) =>this.checkAllTodos(checked))
        eventBus.$off('filterChanged', (filter) =>this.filter=filter)
        eventBus.$off('clearCompletedTodos', () =>this.clearCompleted())
    },
    computed : {
        remaining(){
            return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining (){
            return this.remaining !=0
        },
        todosFiltered() {
            if(this.filter=='all'){
                return this.todos
            }else if (this.filter =='active'){
                return this.todos.filter(todo=> !todo.completed)
            }else if (this.filter=='completed'){
                return this.todos.filter(todo=> todo.completed)
            }
            return this.todos
        },
        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length>0
        }
    },
    methods:{
        addTodo (){
            if(this.newTodo.trim().length==0){
                return
            }

            this.todos.push(
                {
                    id: this.idForTodo,
                    title: this.newTodo,
                    completed: false
                }
            )
            this.newTodo=''
            this.idForTodo++
        },
        removeTodo(index){
            this.todos.splice(index,1)
        },
        checkAllTodos(){
            this.todos.forEach((todo)=> todo.completed=event.target.checked)
        },
        clearCompleted(){
            this.todos=this.todos.filter(todo => !todo.completed)
        },
        finishedEdit(data){
            this.todos.splice(data.index, 1, data.todo)
        }
    }
}
</script>

<style scoped>
.todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
}

.todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;
}
.remove-item:hover{
    color: black;
}
.todo-item-left {
    display: left;
    align-items: center;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}
.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir',Arial, Helvetica, sans-serif;
}
.todo-item-edit:focus {
    outline: none;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
}


</style>