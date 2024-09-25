<template>
  <div>
    <h1>Gestor de Inventario</h1>
    <div class="inputs">
      <h2>Consulta por Nombre del Producto</h2>
      <div>
        <label for="productName">Nombre del Producto:</label>
        <input type="text" v-model="productName" id="productName">
        <button @click="getProductAvailability">Consultar Disponibilidad</button>
      </div>

      <h2>Realizar una compra por nombre del producto y cantidad a comprar</h2>
      <div class="comprarProducto">
        <label for="productName">Nombre del Producto:</label>
        <input type="text" v-model="productName" id="productName">
        <label for="productQuantity">Cantidad a comprar:</label>
        <input type="number" v-model="productQuantity" id="productQuantity">
        <button @click="updateInventory">Comprar</button>
      </div>

    </div>
    <div v-if="product" class="detalleProducto">
      <div v-if="message">{{ message }}</div>
      <h2>Detalles del Producto</h2>
      <p>ID: {{ product.product_id }}</p>
      <p>Nombre: {{ product.name }}</p>
      <p>Cantidad Disponible: {{ product.quantity }}</p>
    </div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      baseUrl: 'https://integracion-fast.onrender.com',
      productName: '',
      product: null,
      message: '',
      productQuantity: 0
    };
  },
  methods: {
    async getProductAvailability() {
      try {
        const response = await axios.get(`${this.baseUrl}/inventory/${this.productName}`);
        console.log("response: ", response);
        this.product = response.data;
        this.message = '';
      } catch (error) {
        this.product = null;
        this.message = error.response.data.message || 'Error al consultar la disponibilidad';
      }
    },
    async updateInventory() {
      try {
        const response = await axios.post(`${this.baseUrl}/inventory/update`, {
          name: this.productName,
          quantity: this.productQuantity 
        });
        console.log("response in update: ", response);
        this.message = response.data.message;
        if (response.status == 200) {
          await this.getProductAvailability(); 
          alert("¡La compra de " + this.quantity + " elementos se ha realizado exitosamente!");
        } else {
          this.product = null;
          alert("¡Error, no hay suficiente stock para realizar esa compra!");
        }
      } catch (error) {
        this.product = null;
        alert("¡Error, no hay suficiente stock para realizar esa compra!");
      }
    }
  }
};
</script>

<style scoped>
.inputs {
  display: flex;
  flex-direction: column;
  height: 40vh;
  padding: 3% 0 3% 0;
  background-color: #a3e8c95c;
  justify-content: space-between;
  align-items: center;
}

.comprarProducto {
  display: flex;
  flex-direction: column;
  width: 50%;
}

.detalleProducto {
  padding: 3% 0 3% 0;
  background-color: rgba(161, 218, 234, 0.359);
}

h1 {
  color: #42b983;
}
</style>