<template>
	<section class="todoapp">
		<header class="header">
			<h1>todos</h1>
			<input
				autocomplete="off"
				placeholder="What needs to be done?"
				class="new-todo"
				v-model="newTodo"
				@keyup.enter="addTodo"
			/>
		</header>

		<List
			:todoList="todoList"
			:filteredTodos="filteredTodos"
			@deleteTodo="deleteTodo"
			@changeAll="changeAll"
		/>

		<footer class="footer" v-show="todoList.length">
			<span class="todo-count">
				<strong>{{ count }}</strong> {{ count <= 1 ? 'item' : 'items' }} left
			</span>
			<ul class="filters">
				<li>
					<a
						href="#/all"
						:class="{ selected: visibility === 'All' }"
						@click="changeState"
						>All
					</a>
				</li>
				<li>
					<a
						href="#/active"
						:class="{ selected: visibility === 'Active' }"
						@click="changeState"
						>Active
					</a>
				</li>
				<li>
					<a
						href="#/completed"
						:class="{ selected: visibility === 'ACompleted' }"
						@click="changeState"
						>Completed
					</a>
				</li>
			</ul>
		</footer>
	</section>

	<footer class="info">
		<p>Double-click to edit a todo</p>
		<p>Written by <a href="http://evanyou.me">Evan You</a></p>
		<p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
	</footer>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import List from './components/List.vue'

let allDone = ref(false)

let visibility = ref('All')

let todoList = ref([])
let id = 0
let newTodo = ref('')
let editTodo = ref('')
let count = computed(() => {
	return todoList.value.filter(todo => {
		return !todo.isCompleted
	}).length
})

let filteredTodos = computed(() => {
	let filter = todoList.value.filter(item => {
		return true
	})
	switch (visibility.value.trim()) {
		case 'All':
			filter = todoList.value.filter(item => {
				return true
			})
			break
		case 'Active':
			filter = todoList.value.filter(item => !item.isCompleted)
			break
		case 'Completed':
			filter = todoList.value.filter(item => item.isCompleted)
			break
	}
	console.log(todoList.value)
	console.log(filter)
	return filter
})

/**
 * 增加一项todo
 */
const addTodo = () => {
	todoList.value.push({
		id: id++,
		value: newTodo.value,
		isCompleted: false
	})
	newTodo.value = ''
}

/**
 * 删除一项todo
 */
const deleteTodo = todo => {
	let index = todoList.value.indexOf(todo)
	todoList.value.splice(index, 1)
}

watch(allDone, () => {
	changeAll()
})

/**
 * 清空或者全选
 */
const changeAll = () => {
	let temp = todoList.value.filter(item => !item.isCompleted).length
	if (temp > 0) {
		todoList.value.forEach(item => {
			item.isCompleted = true
		})
	} else {
		todoList.value.forEach(item => {
			item.isCompleted = false
		})
	}
}

/**
 * 改变显示数据
 */
const changeState = event => {
	visibility.value = event.currentTarget.innerText.trim()
}
</script>

<style>
body {
	font: 14px 'Helvetica Neue', Helvetica, Arial, sans-serif;
	line-height: 1.4em;
	background: #f5f5f5;
	color: #4d4d4d;
	min-width: 230px;
	max-width: 550px;
	margin: 0 auto;
	font-weight: 300;
	display: block;
	position: static;
}

html,
body {
	padding: 0;
}

:focus {
	outline: 0;
}

.todoapp {
	background: #fff;
	margin: 130px 0 40px 0;
	position: relative;
	box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
}

.info {
	margin: 65px auto 0;
	color: #bfbfbf;
	font-size: 10px;
	text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5);
	text-align: center;
}

.info p {
	line-height: 1;
}

.info a {
	color: inherit;
	text-decoration: none;
	font-weight: 400;
}

.info a:hover {
	text-decoration: underline;
}

.todoapp input {
	font-style: italic;
	font-weight: 300;
	/* color: #e6e6e6; */
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

.new-todo {
	padding: 16px 16px 16px 60px;
	border: none;
	background: rgba(0, 0, 0, 0.003);
	box-shadow: inset 0 -2px 1px rgb(0 0 0 / 3%);
	position: relative;
	margin: 0;
	width: 100%;
	font-size: 24px;
	font-family: inherit;
	font-weight: inherit;
	line-height: 1.4em;
	color: inherit;
	box-sizing: border-box;
}

h1 {
	position: absolute;
	top: -155px;
	width: 100%;
	font-size: 100px;
	font-weight: 100;
	text-align: center;
	color: rgba(175, 47, 47, 0.15);
	text-rendering: optimizeLegibility;
}

.main {
	position: relative;
	z-index: 2;
	border-top: 1px solid #e6e6e6;
}

.toggle-all {
	text-align: center;
	border: none;
	opacity: 0;
	position: absolute;
}

.toggle-all + label {
	width: 60px;
	height: 34px;
	font-size: 0;
	position: absolute;
	top: -52px;
	left: -13px;
	-webkit-transform: rotate(90deg);
	transform: rotate(90deg);
}

.toggle-all + label:before {
	content: '❯';
	font-size: 22px;
	color: #e6e6e6;
	padding: 10px 27px 10px 27px;
}

.toggle-all:checked + label:before {
	color: #737373;
}

.todo-list {
	margin: 0;
	padding: 0;
	list-style: none;
}

.todo-list li {
	position: relative;
	font-size: 24px;
	border-bottom: 1px solid #ededed;
}

.todo-list li:last-child {
	border-bottom: none;
}

.todo-list li.editing {
	border-bottom: none;
	padding: 0;
}

.todo-list li.editing .edit {
	display: block;
	width: 506px;
	padding: 12px 16px;
	margin: 0 0 0 43px;
}

.todo-list li.editing .view {
	display: none;
}

.todo-list li .toggle {
	text-align: center;
	width: 40px;
	/* auto, since non-WebKit browsers doesn't support input styling */
	height: auto;
	position: absolute;
	top: 0;
	bottom: 0;
	margin: auto 0;
	border: none; /* Mobile Safari */
	-webkit-appearance: none;
	appearance: none;
}

.todo-list li .toggle {
	opacity: 0;
}

.todo-list li .toggle + label {
	background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23ededed%22%20stroke-width%3D%223%22/%3E%3C/svg%3E');
	background-repeat: no-repeat;
	background-position: center left;
}

.todo-list li .toggle:checked + label {
	background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23bddad5%22%20stroke-width%3D%223%22/%3E%3Cpath%20fill%3D%22%235dc2af%22%20d%3D%22M72%2025L42%2071%2027%2056l-4%204%2020%2020%2034-52z%22/%3E%3C/svg%3E');
}

.todo-list li label {
	word-break: break-all;
	padding: 15px 15px 15px 60px;
	display: block;
	line-height: 1.2;
	transition: color 0.4s;
}

.todo-list li.completed label {
	color: #d9d9d9;
	text-decoration: line-through;
}

.todo-list li .destroy {
	display: none;
	position: absolute;
	top: 0;
	right: 10px;
	bottom: 0;
	width: 40px;
	height: 40px;
	margin: auto 0;
	font-size: 30px;
	color: #cc9a9a;
	margin-bottom: 11px;
	transition: color 0.2s ease-out;
	background: transparent;
	border: 0;
}

.todo-list li .destroy:hover {
	color: #af5b5e;
}

.todo-list li .destroy:after {
	content: '×';
}

.todo-list li:hover .destroy {
	display: block;
}

.todo-list li .edit {
	display: none;
}

.todo-list li.editing:last-child {
	margin-bottom: -1px;
}

.edit {
	position: relative;
	margin: 0;
	width: 100%;
	font-size: 24px;
	font-family: inherit;
	font-weight: inherit;
	line-height: 1.4em;
	color: inherit;
	padding: 6px;
	border: 1px solid #999;
	box-shadow: inset 0 -1px 5px 0 rgba(0, 0, 0, 0.2);
	box-sizing: border-box;
}

.footer {
	color: #777;
	padding: 10px 15px;
	height: 20px;
	text-align: center;
	border-top: 1px solid #e6e6e6;
}

.footer:before {
	content: '';
	position: absolute;
	right: 0;
	bottom: 0;
	left: 0;
	height: 50px;
	overflow: hidden;
	box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6,
		0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6,
		0 17px 2px -6px rgba(0, 0, 0, 0.2);
}

.todo-count {
	float: left;
	text-align: left;
}

.todo-count strong {
	font-weight: 300;
}

.filters {
	margin: 0;
	padding: 0;
	list-style: none;
	position: absolute;
	right: 0;
	left: 0;
}

.filters li {
	display: inline;
}

.filters li a {
	color: inherit;
	margin: 3px;
	padding: 3px 7px;
	text-decoration: none;
	border: 1px solid transparent;
	border-radius: 3px;
}

.filters li a:hover {
	border-color: rgba(175, 47, 47, 0.1);
}

.filters li a.selected {
	border-color: rgba(175, 47, 47, 0.2);
}

.clear-completed {
	float: right;
	position: relative;
	line-height: 20px;
	text-decoration: none;
	cursor: pointer;
	background: transparent;
	border: 0;
	color: #777;
}

html .clear-completed:active {
	float: right;
	position: relative;
	line-height: 20px;
	text-decoration: none;
	cursor: pointer;
	background: transparnet;
	border: 0;
	color: #777;
}

.clear-completed:hover {
	text-decoration: underline;
}
</style>
