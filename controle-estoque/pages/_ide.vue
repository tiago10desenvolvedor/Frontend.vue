<template>
  <v-container>
    <v-form @submit.prevent="submitForm">
      <v-text-field v-model="product.name" label="Nome do Produto" required></v-text-field>
      <v-text-field v-model="product.quantity" label="Quantidade" type="number" required></v-text-field>
      <v-text-field v-model="product.price" label="Preço" type="number" step="0.01" required></v-text-field>
      <v-btn type="submit" color="primary">Atualizar</v-btn>
    </v-form>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      product: {
        name: '',
        quantity: 0,
        price: 0
      },
      loading: false // Para controlar o estado de carregamento
    };
  },
  created() {
    this.fetchProduct();
  },
  methods: {
    // Função para buscar o produto a partir do ID
    fetchProduct() {
  const id = this.$route.params.id;  // Extrai o ID da URL
  console.log('ID do produto:', id);  // Verifique se o ID está correto
  
  if (!id) {
    console.error('ID não encontrado na URL');
    this.showSnackbar('ID inválido', 'error');
    return;  // Se o ID não for encontrado, a função é interrompida
  }

  this.loading = true;
  this.$axios.get(`/products/${id}`)
    .then(response => {
      this.product = response.data;  // Atualiza os dados do produto
    })
    .catch(error => {
      console.error('Erro ao carregar o produto:', error);
      this.showSnackbar('Erro ao carregar o produto.', 'error');
    })
    .finally(() => {
      this.loading = false;
    });
},

submitForm() {
  const id = this.$route.params.id;  // Extrai o ID da URL
  console.log('ID do produto na requisição PUT:', id); // Verifique se o ID está correto
  
  if (!id) {
    console.error('ID inválido');
    this.showSnackbar('ID inválido', 'error');
    return;
  }

  this.$axios.put(`/products/${id}`, this.product)
    .then(() => {
      this.$router.push('/'); // Redireciona após a atualização
    })
    .catch(error => {
      console.error('Erro ao atualizar o produto:', error);
      this.showSnackbar('Erro ao atualizar o produto.', 'error');
    });
},



    // Função para exibir mensagens no Snackbar
    showSnackbar(message, color) {
      this.$root.$emit('snackbar', { message, color });
    }
  }
};
</script>

<style scoped>
/* Estilos específicos da página */
</style>
