<template>
<div>
    <div class="flex my-4">
        <input type="text" class="shadow appearance-none border rounded w-full py-2 px-3 mr-4 text-grey-darker placeholder:text-transparent focus:outline-none focus:ring-2 focus:ring-blue-600 focus:border-transparent" placeholder="Add Todo" v-model="newTodo" @keyup.enter="addTodo">
    </div>
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
        <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="animation">
            <div class="flex mb-4">
                <input type="checkbox" v-model="todo.completed">
                <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="ml-4" >
                    <h5 class="w-64 text-grey-darkest text-xl" :class="{ completed : todo.completed }" >{{ todo.title }}</h5>
                </div>
                <input v-else 
                    type="text"
                    autofocus  
                    class="shadow appearance-none border rounded w-64 mr-4 ml-4 text-grey-darker placeholder:text-transparent focus:outline-none focus:ring-2 focus:ring-blue-600 focus:border-transparent"
                    v-model="todo.title" 
                    @blur="doneEdit(todo)" 
                    @keyup.enter="doneEdit(todo)"
                    @keyup.esc="cancelEdit(todo)"
                >
            <div class="text-black hover:text-yellow cursor-pointer ml-auto" @click="removeTodo(index)">
                &times;
            </div>
        </div>
    </div>
    </transition-group>
    <hr class="h-px my-4 bg-gray-300 border-0">
    <div class="flex justify-between">
        <div class="">
            <label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All</label>
        </div>
        <div class="">
            {{ remaining }} items left
        </div>
    </div>
    <hr class="h-px my-4 bg-gray-300 border-0">
    <div>
        <div class="flex items-center mt-4">
            <button type="button" class="w-full border-l border-t border-b text-base font-medium rounded-l-md text-black bg-white hover:bg-gray-100 px-4 py-2" :class="{ active: filter == 'all' }" @click="filter = 'all'">
                All
            </button>
            <button type="button" class="w-full border text-base font-medium text-black bg-white hover:bg-gray-100 px-4 py-2" :class="{ active: filter == 'active' }" @click="filter = 'active'">
                Active
            </button>
            <button type="button" class="w-full border text-base font-medium text-black bg-white hover:bg-gray-100 px-4 py-2" :class="{ active: filter == 'Completed' }" @click="filter = 'Completed'">
                Completed
            </button>
            <transition name="fade">
                <button v-if="showClearCompletedButton" @click="clearCompleted" type="button" class="w-full border-t border-b border-r text-base font-medium rounded-r-md text-black bg-white hover:bg-gray-100 px-4 py-2 truncate">Make Completed</button>
            </transition>
        </div>
    </div>
</div>
</template>

<script>
export default {
    name: 'todo-list',
    data() {
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
                    'title': 'Be guts.',
                    'completed': false,
                    'editing': false,
                },
            ]
        }
    },
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining() {
            return this.remaining != 0
        },
        todosFiltered() {
            if(this.filter == 'all') {
                return this.todos
            }
            else if (this.filter == 'active') {
                return this.todos.filter(todo => !todo.completed)
            }
            else if (this.filter == 'completed') {
                return this.todos.filter(todo => todo.completed)
            }
        },
        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0
        }
    },
    methods: {
        addTodo() {
            if(this.newTodo.trim().length == 0) {
                return
            }

            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
                editing: false,
            })

            this.newTodo = '',
            this.idForTodo++
        },
        editTodo(todo) {
            this.beforeEditCache = todo.title;
            todo.editing = true;
        },
        doneEdit(todo) {
            if (todo.title.trim() == '') {
                todo.title = this.beforeEditCache
            }
            todo.editing = false;
        },
        cancelEdit(todo) {
            todo.title = this.beforeEditCache
            todo.editing = false;
        },
        removeTodo(index) {
            this.todos.splice(index, 1);
        },
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked)
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)
        }
    },
}
</script>

<style scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.completed {
    text-decoration: line-through;
    color: grey;
}

.animation {
    animation-duration: 0.3s;
}

.fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }

.fade-enter, .fade-leave-to {
opacity: 0;
}
</style>