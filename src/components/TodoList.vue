<template>
    <div>
        <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
        <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
            <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
                <div class="todo-item-left">
                    <input type="checkbox" v-model="todo.completed">
                    <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed : todo.completed }">{{ todo.title }}</div>
                    <input v-else v-focus class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.escape="cancelEdit(todo)">
                </div>
                <div class="remove-item" @click="removeTodo(index)">
                    &times;
                </div>
            </div>
        </transition-group>

        <div class="extra-container">
            <div>
                <label>
                    <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All
                </label>
            </div>
            <div>{{ remaining }} items left</div>
        </div>

        <div class="extra-container">
            <div>
                <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
                <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
                <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
            </div>

            <div>
                <transition name="fade">
                    <button v-if="showClearCompletedButton" @click="clearCompleted">
                        Clear Completed
                    </button>
                </transition>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'todo-list',
    data () {
        return {
            newTodo: '',
            idForTodo: 3,
            beforeEditCache: '',
            filter: 'all',
            todos: [
                {
                    'id': 1,
                    'title': 'Finish Vue Screencast',
                    'completed': false,
                    'editing': false,
                },
                {
                    'id': 2,
                    'title': 'Take over world',
                    'completed': false,
                    'editing': false,
                }
            ]
        }
    },
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length;
        },
        anyRemaining() {
            return this.remaining != 0;
        },
        todosFiltered() {
            if (this.filter == 'all') {
                return this.todos;
            } else if (this.filter == 'active') {
                return this.todos.filter(todo => !todo.completed);
            } else if (this.filter == 'completed') {
                return this.todos.filter(todo => todo.completed);
            }
            return this.todos;
        },
        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0;
        }
    },
    directives: {
        focus: {
            inserted: function(el) {
                el.focus();
            }
        }
    },
    methods: {
        addTodo() {
            if (this.newTodo.trim().length == 0) {
                return;
            }

            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
                editing: false,
            });

            this.newTodo = '';
            this.idForTodo++;
        },
        removeTodo(index) {
            this.todos.splice(index, 1);
        },
        editTodo(todo) {
            this.beforeEditCache = todo.title;
            todo.editing = true;
        },
        doneEdit(todo) {
            if (todo.title.trim() == '') {
                todo.title = this.beforeEditCache;
            }
            todo.editing = false;
        },
        cancelEdit(todo) {
            todo.title = this.beforeEditCache;
            todo.editing = false;
        },
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked);
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed);
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
    @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");

    .todo-input {
        font-size: 18px;
        margin-bottom: 16px;
        padding: 10px 18px;
        width: 100%;

        &:focus {
            outline: 0;
        }
    }

    .todo-item {
        align-items: center;
        animation-duration: .3s;
        display: flex;
        justify-content: space-between;
        margin-bottom: 12px;
    }

    .remove-item {
        cursor: pointer;
        margin-left: 14px;

        &:hover {
            color: #000000;
        }
    }

    .todo-item-left {
        align-items: center;
        display: flex;
    }

    .todo-item-label {
        border: 1px solid #FFFFFF;
        margin-left: 12px;
        padding: 10px;
    }

    .todo-item-edit {
        border: 1px solid #CCCCCC;
        color: #2C3E50;
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        font-size: 24px;
        margin-left: 12px;
        padding: 10px;
        width: 100%;

        &:focus {
            outline: 0;
        }
    }

    .completed {
        color: #808080;
        text-decoration: line-through;
    }

    .extra-container {
        align-items: center;
        border-top: 1px solid lightgrey;
        display: flex;
        font-size: 16px;
        justify-content: space-between;
        margin-bottom: 14px;
        padding-top: 14px;
    }

    button {
        appearance: none;
        background-color: #FFFFFF;
        font-size: 14px;
    
        &:hover {
            background: lightgreen;
        }

        &:focus {
            outline: none;
        }
    }
    
    .active {
        background: lightgreen;
    }

    // CSS Transitions
    .fade-enter-active,
    .fade-leave-active {
        transition: opacity .2s;
    }
    
    .fade-enter,
    .fade-leave-to {
        opacity: 0;
    }
</style>
