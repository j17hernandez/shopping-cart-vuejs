<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      width="200"
    >
      <nuxt-link to="/">
        <v-avatar height="200" class="mt-3" width="200" min-width="200">
          <v-img
            :src="require('assets/img/logo.png')"
            height="200"
            width="200"
            class="text-center"
            style="background-size: contain"
            contain
            alt="logo"
          ></v-img>
        </v-avatar>
      </nuxt-link>
      <v-list>
        <v-list-item-group>
          <v-list-item
            v-for="(item, i) in categories"
            :key="i"
            router
            exact
            @click="changeCategory(item)"
          >
            <v-list-item-action>
              <v-icon>mdi-circle-medium</v-icon>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title v-text="item.name" />
            </v-list-item-content>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app color="primary darken-2" dark>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <div class="mt-6 mr-4">
        <v-text-field
          v-model="search"
          label="Search"
          dense
          outlined
          prepend-inner-icon="mdi-magnify"
          clearable
        />
      </div>
      <v-menu offset-y>
        <template #activator="{ on, attrs }">
          <v-badge class="mr-4" color="green" :content="getCountCart">
            <v-btn icon small v-bind="attrs" v-on="on">
              <v-icon>mdi-cart</v-icon>
            </v-btn>
          </v-badge>
        </template>
        <v-card width="500px">
          <v-card-text>
            <ListCart />
          </v-card-text>
        </v-card>
      </v-menu>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer fixed app>
      <span
        >By
        <a href="https://github.com/j17hernandez" target="_blank">
          j17hernandez</a
        >
        &copy; {{ new Date().getFullYear() }}</span
      >
    </v-footer>
  </v-app>
</template>

<script>
import { mapState } from 'vuex'
import ListCart from '@/components/listaCarrito.vue'
export default {
  name: 'DefaultLayout',
  components: {
    ListCart,
  },
  data() {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      search: '',
      categories: [],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Pet Shop',
    }
  },
  computed: {
    ...mapState(['countCart']),
    getCountCart() {
      const count = this.countCart.toString()
      return count
    },
  },
  watch: {
    search: {
      handler(v) {
        this.$store.commit('setSearch', v)
      },
      deep: true,
    },
  },
  created() {
    this.getCategories()
  },
  methods: {
    // Función para obtener las categorías de productos
    getCategories() {
      this.$axios.get('product-category/').then((resp) => {
        this.categories = resp.data
        this.categories.unshift({ id: 10, name: 'Todos', order: 1 })
        this.changeCategory(resp.data[0])
      })
    },
    // Función para cambiar de categoría
    changeCategory(item) {
      this.$store.commit('setCategory', item)
    },
  },
}
</script>
