<template>
  <div>
    <!-- Modal que permite mostrar el formulario de nuevo producto-->
    <button
      type="button"
      class="btn btn-primary mb-4"
      data-toggle="modal"
      data-target="#exampleModal1"
      @click="clearFields()"
    >
      Añadir Producto
    </button>

    <!-- Modal que muestra el formulario para añadir el producto -->
    <div
      class="modal fade"
      id="exampleModal1"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel" style="color: black">
              Añadir producto
            </h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body" style="color: black">
            <form>
              <div class="form-group">
                <label for="codigo">Código</label>
                <input
                  type="text"
                  class="form-control"
                  id="codigo"
                  placeholder="Introduce el código..."
                  v-model="form_products.codigo"
                />
              </div>
              <div class="form-group">
                <label for="nombre_producto">Nombre del producto</label>
                <input
                  type="text"
                  class="form-control"
                  id="nombre_producto"
                  placeholder="Nombre del producto..."
                  v-model="form_products.nombre_producto"
                />
              </div>
              <div class="form-group">
                <label for="precio">Precio</label>
                <input
                  type="number"
                  class="form-control"
                  id="precio"
                  placeholder="Introduce el precio del producto..."
                  v-model="form_products.precio"
                />
              </div>
              <div class="form-group">
                <label for="stock">Stock Inicial</label>
                <input
                  type="number"
                  class="form-control"
                  id="stock"
                  placeholder="Introduce el stock inicial del producto..."
                  v-model="form_products.stock_inicial"
                />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-primary"
              @click="addProduct()"
              data-dismiss="modal"
            >
              Guardar cambios
            </button>
          </div>
        </div>
      </div>
    </div>

    <table class="table" style="color: white">
      <thead>
        <tr>
          <th scope="col">Código</th>
          <th scope="col">Nombre</th>
          <th scope="col">Precio</th>
          <th scope="col">Stock</th>
          <th scope="col">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <!-- Se iteran los productos con el ciclo for de Vue -->
        <tr v-for="(item, index) in products" :key="index">
          <th scope="row">{{ item.code }}</th>
          <td>{{ item.product_name }}</td>
          <td>{{ item.price }}</td>
          <td>{{ item.stock }}</td>
          <td>
            <!-- Botones de acción para poder editar o borrar el registro correspondiente -->
            <button
              class="btn btn-primary mr-1"
              data-toggle="modal"
              data-target="#exampleModal2"
              @click="getProduct(item.id_product)"
            >
              Editar
            </button>
            <button
              class="btn btn-danger ml-1"
              @click="deleteProduct(item.id_product)"
            >
              Borrar
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <div
      class="modal fade"
      id="exampleModal2"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <!-- Modal que se usa para mostrar el formulario para editar productos -->
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel" style="color: black">
              Editar Producto
            </h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body" style="color: black">
            <form>
              <div class="form-group">
                <label for="codigo">Código</label>
                <input
                  type="text"
                  class="form-control"
                  id="codigo"
                  placeholder="Introduce el código..."
                  v-model="form_products.codigo"
                />
              </div>
              <div class="form-group">
                <label for="nombre_producto">Nombre del producto</label>
                <input
                  type="text"
                  class="form-control"
                  id="nombre_producto"
                  placeholder="Nombre del producto..."
                  v-model="form_products.nombre_producto"
                />
              </div>
              <div class="form-group">
                <label for="precio">Precio</label>
                <input
                  type="number"
                  class="form-control"
                  id="precio"
                  placeholder="Introduce el precio del producto..."
                  v-model="form_products.precio"
                />
              </div>
              <div class="form-group">
                <label for="stock">Stock Inicial</label>
                <input
                  type="number"
                  class="form-control"
                  id="stock"
                  placeholder="Introduce el stock inicial del producto..."
                  v-model="form_products.stock_inicial"
                />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-primary"
              @click="updateProduct()"
              data-dismiss="modal"
            >
              Guardar cambios
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [], //Lista de productos almacenados en la base de datos

      //Objeto que se usa en el formulario para asignar los valores y luego enviarlos
      form_products: {
        id: "",
        codigo: "",
        nombre_producto: "",
        precio: "",
        stock_inicial: "",
      },
    };
  },

  async created() {
    await fetch("http://127.0.0.1:8000/api/showProducts")
      .then((response) => response.json())
      .then((data) => {
        this.products = data;
      });
  },

  methods: {
    /* Función asíncrona que permite añadir un producto con base al objeto del formulario. Se lee cada uno de los campos, se convierten
    a JSON y se envían al backend para luego poder responder. luego limpia los campos del formulario y se refresca la lista de productos. */
    async addProduct() {
      await fetch("http://127.0.0.1:8000/api/addProducts", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(this.form_products),
      })
        .then((data) => {
          if (!data.ok) {
            throw Error(data.status);
          }

          return data.json();
        })
        .then((update) => {
          console.log(update);
          this.form_products.codigo = "";
          this.form_products.nombre_producto = "";
          this.form_products.precio = "";
          this.form_products.stock_inicial = "";
        });

      await fetch("http://127.0.0.1:8000/api/showProducts")
        .then((response) => response.json())
        .then((data) => {
          this.products = data;
        });
    },

    /* Función asíncrona que permite borrar un registro de la base de datos con base al ID del producto. Luego refresca la lista de
    productos. */
    async deleteProduct(product_id) {
      let deleteRequest = {
        id: product_id,
      };

      await fetch("http://127.0.0.1:8000/api/deleteProduct", {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(deleteRequest),
      }).then((data) => {
        if (!data.ok) {
          throw Error(data.status);
        }

        return data.json();
      });

      await fetch("http://127.0.0.1:8000/api/showProducts")
        .then((response) => response.json())
        .then((data) => {
          this.products = data;
        });
    },

    /* Función asíncrona que permite capturar un producto con base a su ID. Se genera una petición de fetch y cuando se capturen
    los datos se empiezan a asignar en los campos del formulario. */
    async getProduct(product_id) {
      let updateRequest = {
        id: product_id,
      };

      await fetch("http://127.0.0.1:8000/api/getProduct", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(updateRequest),
      })
        .then((data) => {
          if (!data.ok) {
            throw Error(data.status);
          }

          return data.json();
        })
        .then((update) => {
          this.form_products.id = update[0].id_product;
          this.form_products.codigo = update[0].code;
          this.form_products.nombre_producto = update[0].product_name;
          this.form_products.precio = update[0].price;
          this.form_products.stock_inicial = update[0].stock;
        });
    },

    /* Función asíncrona que permite actualizar un producto, se manda una petición a la API que contiene los datos del formulario,
    y una vez que la base de datos los capture, limpia los campos del formulario y trata de mostrar los productos de nuevo. */
    async updateProduct() {
      await fetch("http://127.0.0.1:8000/api/updateProduct  ", {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(this.form_products),
      })
        .then((data) => {
          if (!data.ok) {
            throw Error(data.status);
          }

          return data.json();
        })
        .then((update) => {
          console.log(update);
          this.form_products.codigo = "";
          this.form_products.nombre_producto = "";
          this.form_products.precio = "";
          this.form_products.stock_inicial = "";
        });

      await fetch("http://127.0.0.1:8000/api/showProducts")
        .then((response) => response.json())
        .then((data) => {
          this.products = data;
        });
    },

    /* Función hecha específicamente para poder limpiar los cambios del formulario, debido a que usan el mismo v-model.*/
    clearFields() {
      this.form_products.codigo = "";
      this.form_products.nombre_producto = "";
      this.form_products.precio = "";
      this.form_products.stock_inicial = "";
    },
  },
};
</script>
