<script setup>
import { ref } from 'vue'
import { useStore } from 'vuex'
import { computed } from 'vue'
import FormularioEdicao from './EditarCLiente.vue'

const store = useStore()
const clientes = computed(() => store.getters.clientes)
const formAberto = ref(false)
const clienteEditando = ref(null)
const clientesRenderizados = ref([...clientes.value])

const abrirFormularioEdicao = (cliente) => {
  clienteEditando.value = { ...cliente }
  formAberto.value = true
}

const editarCliente = (cliente) => {
  if (
    (cliente.ativo.toLowerCase() !== 'sim' && cliente.ativo.toLowerCase() !== 'não') ||
    (cliente.produtoAtivo.toLowerCase() !== 'sim' && cliente.produtoAtivo.toLowerCase() !== 'não')
  ) {
    alert('Status inválido. Por favor digita "Sim" ou "Não" !')
  } else {
    store.dispatch('editarCliente', cliente)
    formAberto.value = false
  }
}

const deletarCliente = (clienteId) => {
  store.dispatch('deletarCliente', clienteId)
  clientesRenderizados.value = [...clientes.value]
}
</script>
<template>
  <div class="container table-responsive">
    <h2 class="title">Clientes</h2>
    <p>Para alterar o status do seu cliente e o produto do mesmo, basta clicar em editar.</p>
    <FormularioEdicao
      v-if="formAberto"
      :cliente="clienteEditando"
      @fechar="formAberto = false"
      @salvar="editarCliente"
    />
    <table class="table">
      <thead>
        <tr>
          <th scope="col">Cliente Ativo</th>
          <th scope="col">Nome</th>
          <th scope="col">Telefone</th>
          <th scope="col">Documento</th>
          <th scope="col">E-mail</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="cliente in clientesRenderizados" :key="cliente.id">
          <td scope="row">
            {{ cliente.ativo }}
            <button class="btn btn-info" @click="abrirFormularioEdicao(cliente)">Editar</button>
            <button class="btn btn-danger" @click="deletarCliente(cliente.id)">Deletar</button>
          </td>
          <td>{{ cliente.nome }}</td>
          <td>{{ cliente.telefone }}</td>
          <td>{{ cliente.documento }}</td>
          <td>{{ cliente.email }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<style lang="scss" scoped>
@import './Styles/variaveis.scss';

.title {
  @include styleTitle();
}
</style>
