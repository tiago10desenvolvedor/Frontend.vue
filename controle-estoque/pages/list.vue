<template>
  <v-app>
    <v-container>
      <v-btn @click="goToAddProduct" color="primary">Adicionar Produto</v-btn>
      <v-data-table :headers="headers" :items="products" item-key="id">
  <template v-slot:items="props">
    <tr>
      <td>{{ props.item.name }}</td>
      <td>{{ props.item.quantity }}</td>
      <td>{{ props.item.price }}</td>
      <td>
        <v-btn @click="editProduct(props.item.id)" color="yellow" small>Editar</v-btn>
        <v-btn @click="openDeleteDialog(props.item.id)" color="red" small>Excluir</v-btn>
      </td>
    </tr>
  </template>
</v-data-table>
      <v-snackbar v-model="snackbar.show" :color="snackbar.color" timeout="3000">
        {{ snackbar.message }}
      </v-snackbar>
      <v-dialog v-model="dialog" persistent max-width="290">
        <v-card>
          <v-card-title class="headline">Confirmar Exclusão</v-card-title>
          <v-card-text>Tem certeza que deseja excluir este produto?</v-card-text>
          <v-card-actions>
            <v-btn color="blue darken-1" text @click="dialog = false">Cancelar</v-btn>
            <v-btn color="red darken-1" text @click="confirmDelete">Excluir</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-container>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      headers: [
        { text: 'Nome', value: 'name' },
        { text: 'Quantidade', value: 'quantity' },
        { text: 'Preço', value: 'price' },
        { text: 'Ações', value: 'actions', sortable: false }
      ],
      snackbar: {
        show: false,
        message: '',
        color: ''
      },
      dialog: false,
      productToDelete: null
    };
  },
  created() {
    this.fetchProducts(); 
  },
  methods: {
    
    fetchProducts() {
      console.log('Requisição para:', 'http://localhost:5201/api/products');
      this.$axios.get('http://localhost:5201/api/products')
        .then(response => {
          console.log('Produtos carregados:', response.data);
          this.products = response.data;  
        })
        .catch(error => {
          console.error('Erro ao carregar os produtos:', error.response ? error.response.data : error);
          this.snackbar.show = true;
          this.snackbar.message = 'Erro ao carregar os produtos.';
          this.snackbar.color = 'error';
        });
    },
    goToAddProduct() {
      this.$router.push('/add-product');  
    },
    editProduct(id) {
      console.log('Editar produto com ID:', id);  
      this.$router.push(`/edit-product/${id}`);  
    },
    openDeleteDialog(id) {
      console.log('Abrindo diálogo de exclusão para o produto com ID:', id); 
      this.productToDelete = id;  
      this.dialog = true;  
    },
    confirmDelete() {
      this.deleteProduct(this.productToDelete);  
      this.dialog = false;  
    },
    deleteProduct(id) {
      console.log('Excluindo produto com ID:', id);  
      this.$axios.delete(`http://localhost:5201/api/products/${id}`)
        .then(() => {
          this.fetchProducts();  
          this.snackbar.show = true;
          this.snackbar.message = 'Produto excluído com sucesso!';
          this.snackbar.color = 'success';
        })
        .catch(error => {
          console.error('Erro ao excluir o produto:', error.response ? error.response.data : error);
          this.snackbar.show = true;
          this.snackbar.message = 'Erro ao excluir o produto.';
          this.snackbar.color = 'error';
        });
    },

    
    validateProductForm(product) {
      if (!product.name || product.price < 0 || product.quantity < 0) {
        this.snackbar.show = true;
        this.snackbar.message = 'Verifique se todos os campos estão preenchidos corretamente.';
        this.snackbar.color = 'error';
        return false;
      }
      return true;
    }
  }
};
</script>

<style>
.v-btn {
  visibility: visible !important;
  display: inline-block !important;
}

</style>
