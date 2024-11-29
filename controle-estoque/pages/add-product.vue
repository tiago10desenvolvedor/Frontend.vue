<template>
  <v-container>
    <!-- Formulário de adição de produto -->
    <v-form @submit.prevent="submitForm">
      <v-text-field v-model="product.name" label="Nome do Produto" required></v-text-field>
      <v-text-field v-model="product.quantity" label="Quantidade" type="number" required></v-text-field>
      <v-text-field v-model="product.price" label="Preço" type="number" step="0.01" required></v-text-field>
      <v-btn type="submit" color="primary">Salvar</v-btn>
    </v-form>

    <!-- Lista de produtos -->
    <v-list>
      <v-list-item-group v-if="products.length > 0">
        <v-list-item v-for="(product, index) in products" :key="index">
          <v-list-item-content>
            <v-list-item-title>{{ product.name }}</v-list-item-title>
            <v-list-item-subtitle>{{ product.quantity }} - R$ {{ product.price }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </v-list-item-group>
      <v-list-item v-else>
        <v-list-item-content>
          <v-list-item-title>Não há produtos disponíveis.</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>
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
      products: []
    };
  },
  methods: {
    // Função para adicionar um produto
    submitForm() {
      this.$axios.post('http://localhost:5201/api/products', this.product)
        .then(() => {
          this.fetchProducts();  // Atualiza a lista de produtos
          this.$router.push('/');  // Redireciona para a página principal
        })
        .catch(error => console.error('Erro ao salvar o produto:', error));
    },

    // Função para buscar todos os produtos
    fetchProducts() {
      this.$axios.get('http://localhost:5201/api/products')
        .then(response => {
          this.products = response.data;  // Atualiza a lista de produtos
        })
        .catch(error => console.error('Erro ao carregar os produtos:', error));
    }
  },
  created() {
    this.fetchProducts();  // Carrega os produtos ao iniciar
  }
};
</script>

<style scoped>
/* Estilos específicos da página */
</style>
