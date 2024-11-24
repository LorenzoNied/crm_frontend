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
                        <v-btn  @click="novo()" class="mb=2" color="primary" dark v-bind="props">Cliente</v-btn>
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
            <v-container class="py-4 px-6">
              <!-- Botão de Adicionar Pedido -->
              <v-row justify="center" align="center" class="mb-4">
                <v-btn color="primary" @click="abrirPedido = true" elevation="2">
                  Adicionar Pedido
                </v-btn>
              </v-row>

              <!-- Tabela de Pedidos -->
              <v-data-table 
                :items="pedidos"
                class="mx-auto"
                style="max-width: 800px; border: 1px solid #ccc; border-radius: 10px; box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);"
              ></v-data-table>
            </v-container>
          </v-tabs-window-item>
            <v-dialog v-model="abrirPedido" max-width="600px" class="pedido-popup">
              <v-card>
                <v-card-title class="headline">Informações do Pedido</v-card-title>
                <v-card-text>
                  <v-select
                    v-model="clienteSelecionado"
                    :items="clientes"
                    item-title="nome" 
                    item-value="id"
                    label="Selecione um Cliente"
                    outlined
                  ></v-select>
                  <v-select
                    v-model="orderStatus"
                    :items="statusOptions"
                    label="Status do Pedido"
                    outlined
                    class="mt-4"
                  ></v-select>
                  <v-text-field
                    v-model="orderValue"
                    label="Valor do Pedido"
                    prefix="R$"
                    outlined
                    class="mt-4"
                  ></v-text-field>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue" @click="salvarPedido">Salvar Pedido</v-btn>
                  <v-btn color="red" @click="abrirPedido = false">Cancelar</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
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
  listarPedidos();
});

const pedidos = ref ([]);
const abrirPedido = ref(false);
const statusOptions = ref(['Pendente', 'Em andamento', 'Concluído']);

const clienteSelecionado = ref(null);
const orderStatus = ref('');
const orderValue = ref(null);


const listarPedidos = async () => {
  const response = await axios.get("http://localhost:8080/pedido");
  pedidos.value = response.data;
};

const salvarPedido = async () => {

  const pedido = {
    idCliente: clienteSelecionado.value,
    status: orderStatus.value,
    valor: orderValue.value
  };

  try {
    const response = await axios.post('http://localhost:8080/pedido', pedido);

    if (response.status >= 200 && response.status < 300) {
      mensagem.value = 'Pedido criado com sucesso!';
      tipoMensagem.value = 'success';

      setTimeout(() => {
        mensagem.value = '';
      }, 5000);

      abrirPedido.value = false;
    }
  } catch (error) {
    mensagem.value = error.response?.data || 'Ocorreu um erro ao salvar o pedido.';
    tipoMensagem.value = 'error';

    setTimeout(() => {
      mensagem.value = '';
    }, 5000);
  }
};


const clientes = ref([]);
const cliente = ref({})
const dialogNovoCliente = ref(false)

const tab = ref(0);
const dialogClienteID = ref(false);
const dialogExcluirCliente = ref(false);

const mensagem = ref('');
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

    if (response.status >= 200 && response.status < 300) {
      mensagem.value = response.data || 'Cliente salvo com sucesso!';
      tipoMensagem.value = 'success';

      setTimeout(() => {
        mensagem.value = '';
      }, 5000);

      listarClientes();
      dialogNovoCliente.value = false;
    }
  } catch (error) {
    mensagem.value = error.response?.data || 'Ocorreu um erro ao salvar o cliente.';
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
  try{
    let  response;

    response = await axios.delete(`http://localhost:8080/cliente/${e.id}`);
    clientes.value = clientes.value.filter(cliente => cliente.id !== e.id); 
    dialogExcluirCliente.value = false;
    dialogClienteID.value = false;

    mensagem.value = response.data || 'Cliente deletado!';
    tipoMensagem.value = 'success';

    setTimeout(() => {
        mensagem.value = '';
      }, 5000);

  } catch (error) {
    mensagem.value = error.response?.data || 'Ocorreu um erro ao deletar o cliente.';
    tipoMensagem.value = 'error';

    setTimeout(() => {
      mensagem.value = '';
    }, 5000);
  }
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

.pedido-popup .v-card {
  background-color: black;
  color: white;
}

</style>