<template>
  <v-app>
    <v-main>
      <v-card>
        <v-tabs v-model="tab" align-tabs="center" bg-color="orange" stacked>
          <v-tab>
            <v-icon icon="mdi-account"></v-icon>
            Cliente
          </v-tab>
          <v-tab>
            <v-icon icon="mdi-shopping"></v-icon>
            Produtos
          </v-tab>

          <v-tab>
            <v-icon icon="mdi-cart"></v-icon>
            Pedidos
          </v-tab>
        </v-tabs>

        <v-tabs-window v-model="tab">
          <v-tabs-window-item>
            <v-card>
              <v-card-text>
                <v-btn @click="novo()" color="light-green-darken-4" icon="mdi-plus" variant="tonal"></v-btn>
              </v-card-text>
            </v-card>
            <div>
              <v-data-table :items="clientes"></v-data-table>
            </div>
          </v-tabs-window-item>

          <v-dialog v-model="dialogCliente" width="500">
            <v-card>
              <v-card-text> Cadastro de Cliente
                <v-text-field v-model="cliente.nome" label="Nome do cliente"/>
                <v-text-field v-model="cliente.cpf" label="CPF do cliente"  maxlength="11" />
                <v-text-field v-model="cliente.telefone" label="numero de Telefone"/>
              
              </v-card-text>
              <v-card-text> Endereço do Cliente
                <v-text-field v-model="cliente.endereco.rua" label="Nome da rua"/>
                <v-text-field v-model="cliente.endereco.numero" label="Numero da residencia"/>
                <v-text-field v-model="cliente.endereco.bairro" label="Nome do bairro"/>
                <v-text-field v-model="cliente.endereco.cidade" label="Cidade"/>
              </v-card-text>
              <v-card-actions>
                <v-btn color="red" text @click="dialogCliente = false">Cancelar</v-btn>
                <v-btn color="green" text @click="salvarCliente">Adicionar</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>

          <v-tabs-window-item>
            <v-card class="mx-auto" max-width="400">
              <v-card class="mx-auto" max-width="400" elevation="3" rounded>
                <v-img
                  class="align-end text-white"
                  height="200"
                  src="https://leadster.com.br/blog/wp-content/uploads/2023/04/O-que-e-o-marketing-de-produto.webp"
                  cover
                >
                  <v-card-title class="font-weight-bold" style="background-color: rgba(0, 0, 0, 0.5);">Produtos</v-card-title>
                </v-img>
              </v-card>
              <v-card-actions>
                <v-row justify="center" align="center">
                  <v-col cols="auto">
                    <v-btn color="orange" variant="outlined" @click="dialog = true">
                      Adicionar Produto
                    </v-btn>
                  </v-col>
                </v-row>
              </v-card-actions>
            </v-card>
          </v-tabs-window-item>

          <v-tabs-window-item>
            <v-card>
              <v-card-text> produtos </v-card-text>
            </v-card>
          </v-tabs-window-item>
        </v-tabs-window>
      </v-card>

      <v-dialog v-model="a" max-width="500px">
        <v-card>
          <v-card-title class="text-h5">Adicionar Produto</v-card-title>
          <v-card-text>
            <v-text-field label="Nome do Produto" v-model="produto.nome"></v-text-field>
            <v-text-field label="Preço" v-model="produto.preco" prefix="R$"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue" text @click="dialog = false">Cancelar</v-btn>
            <v-btn color="orange" text @click="adicionarProduto">Adicionar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
onMounted(() => {
  listarClientes();
});

const clientes = ref([]);

const tab = ref(0);
const dialogCliente = ref(false);


const listarClientes = async () => {
  const response = await axios.get("http://localhost:8080/cliente");
  clientes.value = response.data;
};

const cliente = ref ({
  nome: '',
  cpf: '',
  telefone: '',
  endereco: {
    rua: '',
    numero: '',
    bairro: '',
    cidade: ''
  }
})

const novo = () => {
  cliente.value = {nome: '',
    cpf: '',
    telefone: '',
    endereco: {
      rua: '',
      numero: '',
      bairro: '',
      cidade: ''}
    }
  dialogCliente.value = true;
};

const salvarCliente = async () =>{
  await axios.post(`http://localhost:8080/cliente`, cliente.value)
  dialogCliente.value = false;
  listarClientes();
};


</script>