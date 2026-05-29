<template>
    <main class="contenedor">
        <h1>Inventario de libros</h1>

        <form class="formulario" @submit.prevent="guardarLibro">
            <input v-model="libro.titulo" type="text" placeholder="Título del libro" required />

            <input v-model="libro.autor" type="text" placeholder="Autor" required />

            <input v-model="libro.precio" type="number" placeholder="Precio" required />

            <button type="submit">
                {{ editando ? 'Actualizar libro' : 'Guardar libro' }}
            </button>
        </form>

        <table>
            <thead>
                <tr>
                    <th>Título</th>
                    <th>Autor</th>
                    <th>Precio</th>
                    <th>Acciones</th>
                </tr>
            </thead>

            <tbody>
                <tr v-for="item in libros" :key="item.id">
                    <td>{{ item.titulo }}</td>
                    <td>{{ item.autor }}</td>
                    <td>${{ item.precio }}</td>
                    <td>
                        <button @click="editarLibro(item)">Editar</button>
                        <button @click="eliminarLibro(item.id)">Eliminar</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </main>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const API_URL = 'http://localhost:3000/libros'

const libros = ref([])
const editando = ref(false)
const idEditando = ref(null)

const libro = ref({
    titulo: '',
    autor: '',
    precio: '',
})

const obtenerLibros = async () => {
    const respuesta = await axios.get(API_URL)
    libros.value = respuesta.data
}

const guardarLibro = async () => {
    if (editando.value) {
        await axios.put(`${API_URL}/${idEditando.value}`, libro.value)
        editando.value = false
        idEditando.value = null
    } else {
        await axios.post(API_URL, libro.value)
    }

    libro.value = {
        titulo: '',
        autor: '',
        precio: '',
    }

    obtenerLibros()
}

const editarLibro = (item) => {
    editando.value = true
    idEditando.value = item.id

    libro.value = {
        titulo: item.titulo,
        autor: item.autor,
        precio: item.precio,
    }
}

const eliminarLibro = async (id) => {
    await axios.delete(`${API_URL}/${id}`)
    obtenerLibros()
}

onMounted(() => {
    obtenerLibros()
})
</script>