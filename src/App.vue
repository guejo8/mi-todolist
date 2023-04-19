<script setup>
// Importamos las funciones y objetos que vamos a utilizar de la librería Vue.js
import { ref, onMounted, computed, watch } from 'vue'

// Creamos una referencia reactiva para almacenar las tareas
const todos = ref([])

// Creamos una referencia reactiva para almacenar el nombre del usuario
const name = ref('')

// Creamos una referencia reactiva para almacenar el contenido del campo de texto
const input_content = ref('')

// Creamos una referencia reactiva para almacenar la categoría seleccionada
const input_category = ref(null)

// Creamos una variable calculada que ordena las tareas por fecha de creación
const todos_asc = computed(() => {
	// Ordenamos las tareas por fecha de creación utilizando el método sort()
	return todos.value.sort((a,b) =>{
		return a.createdAt - b.createdAt
	})
})

// Registramos un watcher para guardar el nombre del usuario en localStorage
watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

// Registramos un watcher para guardar las tareas en localStorage
watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

// Función para agregar una nueva tarea a la lista
const addTodo = () => {
	// Verificamos que el contenido del campo de texto y la categoría no estén vacíos
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	// Agregamos la nueva tarea al arreglo de tareas
	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime() // Agregamos la fecha de creación de la tarea
	})
}

// Función para eliminar una tarea de la lista
const removeTodo = (todo) => {
	// Filtramos el arreglo de tareas para remover la tarea seleccionada
	todos.value = todos.value.filter((t) => t !== todo)
}

// Función que se ejecuta cuando el componente ha sido montado en el DOM
onMounted(() => {
	// Recuperamos el nombre del usuario desde localStorage, si existe
	name.value = localStorage.getItem('name') || ''

	// Recuperamos las tareas desde localStorage, si existen
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="Tu nombre aqui" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="ej.Estudiar partitura"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
