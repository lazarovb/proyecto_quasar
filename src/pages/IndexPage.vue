<template>
  <q-page padding>
    <q-table title="Treats"
    :rows="posts"
    :columns="columns"
    row-key="name"
    >
    <template v-slot:top>
      <span class="text-h5">Articulos</span>
      <q-space />
      <q-btn color="primary" label="Nuevo" :to = "{ name: 'formPost'}"/>
    </template>

    <template v-slot:body-cell-actions="props">
      <q-td :props="props" class="q-gutter-sm">
        <q-btn icon="edit" color="info" dense size="sm" @click="handleEditPost(props.row.id)"/>
        <q-btn icon="delete" color="negative" dense size="sm" @click="handleDeletePost(props.row.id)"/>
      </q-td>
    </template>
   </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'
/* import { router } from 'quasar/wrappers' */

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'Title', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'Autor', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Acciones', align: 'right' }
    ]

    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }
    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Remover',
          message: 'Desea remover este items?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Removido con éxito', icon: 'ckeck', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Error al remover', icon: 'times', color: 'negative' })
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
