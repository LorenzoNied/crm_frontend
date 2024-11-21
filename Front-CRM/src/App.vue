<template>
  <v-app>
    <v-main>
      <div v-if="mensagem" :class="['notification', tipoMensagem]">
        {{ mensagem }}
        
      </div>
      <v-card>
        <v-tabs v-model="tab" align-tabs="center" bg-color="orange" stacked>
          <v-tab>
            <v-icon icon="mdi-account"></v-icon>
            Cliente
          </v-tab>

          <v-tab>
            <v-icon icon="mdi-cart"></v-icon>
            Pedidos
          </v-tab>
        </v-tabs>

        <v-tabs-window v-model="tab">
          <v-tabs-window-item>
           
            <div>
              <v-data-table :headers="dessertheaders" :items="clientes" style="max-width: 1300px; overflow-x: auto; margin: auto; display: block;">
                <template v-slot:top>
                  <v-toolbar flat>
                    <v-toolbar-title>CLIENTES</v-toolbar-title>
                    <v-divider class="mx-4" insert vertical></v-divider>
                    <v-spacer></v-spacer>
                    <v-dialog v-model ="dialogNovoCliente">
                      <template v-slot:activator="{ props }">
                        <v-btn  @click="novo()" class="mb=2" color="primary" dark v-bind="props">Novo Cliente</v-btn>
                      </template>
                      <v-card>
                        <v-card-text>
                          <v-container>
                            Novo Cliente
                            <v-row>
                              <v-col>
                                <v-text-field v-model="cliente.nome" label="Nome do cliente"/>
                              </v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="12" md="4" sm="6">
                                <v-text-field v-model="cliente.cpf" label="CPF do cliente"  maxlength="11"/>
                              </v-col>
                              <v-col cols="12" md="4" sm="6">
                                <v-text-field v-model="cliente.telefone" label="numero de Telefone" maxlength="11"/>
                              </v-col>
                            </v-row>
                            Endereço
                            <v-row>
                              <v-col cols="12" md="4" sm="8">
                                <v-text-field v-model="cliente.endereco.rua" label="Nome da rua"/>
                              </v-col>
                              <v-col cols="12" md="4" sm="4">
                                <v-text-field v-model="cliente.endereco.numero" label="Numero da residencia"/>
                              </v-col>
                              <v-col cols="12" md="4" sm="6">
                                <v-text-field v-model="cliente.endereco.bairro" label="Nome do bairro"/>
                              </v-col>
                              <v-col cols="12" md="4" sm="6">
                                <v-text-field v-model="cliente.endereco.cidade" label="Cidade"/>
                              </v-col>
                            </v-row>
                          </v-container>
                        </v-card-text>
                        <v-card-actions>
                          <v-spacer></v-spacer>
                          <v-btn color="red" variant="text" @click="cancelar"> Cancelar </v-btn>
                          <v-btn color="green" text @click="salvarCliente">Salvar</v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                  </v-toolbar>
                </template>
                <template v-slot:item.actions="{ item }">
                  <v-icon class="me-2" size="small" @click="listarClientesID(item.id)">mdi-menu</v-icon>
                </template>
              </v-data-table>
            </div>
          </v-tabs-window-item> 

          <v-dialog v-model="dialogClienteID" width="800">
            <v-card>
              <v-card-text > 
                <v-icon icon="mdi-account"></v-icon>
                Cliente {{ cliente.nome }}
                <v-btn @click="atualizarCliente(cliente)" color="blue" class="position-absolute" style="right: 140px;">
                  <v-icon icon="mdi-pencil"></v-icon> Editar
                </v-btn>
                <v-btn @click="confirmacaoDeExclusao()" color="red" class="position-absolute" style="right: 15px;">
                  <v-icon icon="mdi-close-circle"></v-icon>
                  Excluir</v-btn>
              </v-card-text>
                <v-container>Dados
                  <v-sheet class="d-flex flex-wrap bg-surface-variant">CPF
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.cpf }}
                    </v-sheet>Telefone
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.telefone }}
                    </v-sheet>
                  </v-sheet>
                  Endereço
                  <v-sheet class="d-flex flex-wrap bg-surface-variant">Rua
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.endereco.rua }}
                    </v-sheet> Numero
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.endereco.numero }}
                    </v-sheet>
                  </v-sheet>
                  <v-sheet class="d-flex flex-wrap bg-surface-variant">Bairro
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.endereco.bairro }}
                    </v-sheet>Cidade
                    <v-sheet class="flex-1-0 ma-2 pa-2">
                      {{ cliente.endereco.cidade }}
                    </v-sheet>
                  </v-sheet>
                </v-container>
            </v-card>
          </v-dialog>

          <v-dialog v-model="dialogExcluirCliente" max-width="500px">
            <v-card>
            <v-card-title class="text-h5 text-center">Você realmente quer excluir este cliente?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="fecharConfirmacao">cancelar</v-btn>
              <v-btn color="red" variant="text" @click="exluirCliente(cliente)">Excluir</v-btn>
              <v-spacer></v-spacer>
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
                  cover>
                  <v-card-title class="font-weight-bold" style="background-color: rgba(0, 0, 0, 0.5);">Pedidos</v-card-title>
                </v-img>
              </v-card>
              <v-card-actions>
                <v-row justify="center" align="center">
                  <v-col cols="auto">
                    <v-btn color="orange" variant="outlined" @click="dialog = true">
                      Adicionar Pedido
                    </v-btn>
                  </v-col>
                </v-row>
              </v-card-actions>
            </v-card>
          </v-tabs-window-item>
        </v-tabs-window>
      </v-card>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, onMounted, computed, nextTick, watch} from 'vue';
import axios from 'axios';
onMounted(() => {
  listarClientes();
});

const clientes = ref([]);
const cliente = ref({})
const dialogNovoCliente = ref(false)

const tab = ref(0);
const dialogClienteID = ref(false);
const dialogExcluirCliente = ref(false);

const mensagem = ref({});
const tipoMensagem = ref('');

const listarClientes = async () => {
  const response = await axios.get("http://localhost:8080/cliente");
  clientes.value = response.data;
};

const listarClientesID = async (clienteID) => {
  const response = await axios.get(`http://localhost:8080/cliente/${clienteID}`)
  cliente.value = response.data;
  dialogClienteID.value = true;
}

const dessertheaders = ref([{
  align:'start',
  sortable: false,
  key: 'name',
},
  {title: 'Nome', key: 'nome'},
  {title: 'CPF', key: 'cpf'},
  {title: 'Actions', key: 'actions', sortable: false},

])

const novo = () => {
  cliente.value = {
    id: null,
    nome: '',
    cpf: '',
    telefone: '',
    endereco: {
      rua: '',
      numero: '',
      bairro: '',
      cidade: ''}
    }
};

const cancelar = () => {
  dialogNovoCliente.value = false
}

const salvarCliente = async () =>{
  try{
    let  response;
    if(cliente.value.id){
      response = await axios.put(`http://localhost:8080/cliente/${cliente.value.id}`, cliente.value)
    
    } else {
      response = await axios.post(`http://localhost:8080/cliente`, cliente.value)
    }

    mensagem.value = response.data.message || 'Mensagem padrão';
    tipoMensagem.value='success';

    setTimeout(() => {
      mensagem.value = '';
    }, 5000);
    listarClientes();
    dialogNovoCliente.value = false;
  } catch (error) {
    mensagem.value = error.response?.data?.message || 'Ocorreu um erro ao salvar o cliente.';
    tipoMensagem.value = 'error';

    setTimeout(() => {
      mensagem.value = '';
    }, 5000);
  }
};

function atualizarCliente (e){
  cliente.value = e;
  dialogNovoCliente.value = true;
  
}

const fecharConfirmacao = () => {
  dialogExcluirCliente.value = false;
}

const confirmacaoDeExclusao = () => {
  dialogExcluirCliente.value = true;
}

const exluirCliente = async (e) => {
  await axios.delete(`http://localhost:8080/cliente/${e.id}`);
  clientes.value = clientes.value.filter(cliente => cliente.id !== e.id); 
  dialogExcluirCliente.value = false;
  dialogClienteID.value = false;
}
</script>

<style scoped>
.notification {
  padding: 10px;
  border-radius: 5px;
  margin-top: 20px;
  text-align: center;
  font-size: 16px;
  font-weight: bold;
}

.success {
  background-color: green;
  color: white;
}

.error {
  background-color: red;
  color: white;
}
</style>