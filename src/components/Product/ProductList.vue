<template>
  <div>
    <navbar :pass_carts_length="cart_length" />

    <link
      href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"
      rel="stylesheet"
      id="bootstrap-css"
    />
    <br />
    <h1>Danh sách sản phẩm</h1>
    <br />

    <div class="row">
      <div class="col-sm-4"></div>
      <div class="col-sm-4"></div>
      <div class="col-sm-4">
        <b-input-group class="mt-3">
          <b-form-input
            type="text"
            v-model="searchvalue"
            placeholder="Nhập tên sản phẩm"
          >
          </b-form-input>
        </b-input-group>
      </div>
    </div>

    <br />

    <div class="row">
      <div class="col-sm-8"></div>
      <div class="col-sm-2"></div>
      <div class="col-sm-2">
        <div class="input-group-append">
          <select class="form-select" v-model="sortvalue" @change="sortprop()">
            <option value="" disabled selected>Sắp xếp theo</option>
            <option value="blank">---</option>
            <option value="asc">Giá tăng dần</option>
            <option value="desc">Giá giảm dần</option>
          </select>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">
        <div
          class="col-lg-4 d-flex align-items-stretch"
          v-for="product in filterProduct"
          v-bind:key="product.id"
        >
          <div class="card">
            <h6 class="card-header">{{ product.name }}</h6>
            <div class="card-body" style="height: 150px">
              <img
                :src="product.imagePath"
                class="img-circle"
                alt="Services"
              />
            </div>
            <br />
            <div class="card-footer">
              <span class="text-muted"
                >₫{{ product.price.toLocaleString() }}</span
              >
              <router-link
                :to="{
                  name: 'ProductDetail',
                  params: { id: product.id },
                }"
                class="btn btn-secondary btn-sm float-right"
                >Xem mặt hàng
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  width: 250px;
  border-radius: 5px;
  padding: 10px;
  transition: 0.3s;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);

  display: flex;
  align-items: center;
  justify-content: center;

  margin-top: 50px;
}
.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}
img {
  border-radius: 20px 20px 0 0;
  height: 150px;
  width: 200px;
}
.margin-card {
  text-align: center;
  margin-top: 20px;
}
.input-group.md-form.form-sm.form-1 input {
  border: 1px solid #bdbdbd;
  border-top-right-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
}
input:placeholder-shown {
  font-style: italic;
}
.dropdown {
  display: flex;
  justify-content: flex-end;
}
</style>

<script>
import ProductService from "@/api-services/ProductService";
import CartService from "@/api-services/CartService";
import Navbar from "../Navbar.vue";
import ProductDetail from "./ProductDetail.vue";

export default {
  components: {
    Navbar,
  },
  name: "ProductList",
  data() {
    return {
      products: [],
      searchvalue: "",
      sortvalue: "",
      cart_length: 0,
    };
  },
  created() {
    ProductService.getAll().then((response) => {
      this.products = response.data;
    });

    CartService.getCart().then((response) => {
      this.cart_length = response.data.length;
      console.log(this.cart_length);
    });
  },

  computed: {
    filterProduct() {
      return this.products.filter((product) =>
        product.name
          .toLowerCase()
          .includes(this.searchvalue.toLowerCase())
      );
    },
  },
  methods: {
    sortprop() {
      if (this.sortvalue === "asc") {
        this.products.sort((a, b) =>
          a.price < b.price ? -1 : 1
        );
      } else if (this.sortvalue === "desc") {
        this.products.sort((a, b) =>
          a.price > b.price ? -1 : 1
        );
      }
    },

    addToCart(id) {
      CartService.addToCart(id).then((response) => {
        this.cart_length = response.data.length;
        console.log("product list (cart-length): " + this.cart_length);
      });
    },
  },
}; 
</script>