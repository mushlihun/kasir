<template>
  <v-app id="inspire">
    <v-navigation-drawer
      dark
      color="#5B86E5"
      v-model="drawer"
      :clipped="$vuetify.breakpoint.lgAndUp"
      app
      elevation="4"
    >

      <template v-slot:prepend>
        <v-list-item two-line>
          <v-list-item-avatar>
            <v-icon>mdi-face</v-icon>
          </v-list-item-avatar>

          <v-list-item-content>
            <v-list-item-title>{{ firstName }} {{ lastName }}</v-list-item-title>
            <v-list-item-subtitle>Logged In</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
      </template>

      <v-list>
        <v-list-item
          link
          href="/home"
        >
          <v-list-item-icon>
            <v-icon>mdi-view-dashboard-outline</v-icon>
          </v-list-item-icon>
          <v-list-item-title>Home Page</v-list-item-title>
        </v-list-item>

        <v-list-group
          prepend-icon="mdi-note-text-outline"
          value
        >
          <template v-slot:activator>
            <v-list-item-title>Order</v-list-item-title>
          </template>

          <v-list-item
            link
            href="/home/order/create"
          >
            <v-list-item-title>Create Order</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-note-plus-outline</v-icon>
            </v-list-item-icon>
          </v-list-item>

          <v-list-item
            link
            href="/home/order/list"
          >
            <v-list-item-title>Order List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-note-text-outline</v-icon>
            </v-list-item-icon>
          </v-list-item>

          <v-list-item
            link
            href="/home/order/cancelled/list"
          >
            <v-list-item-title>Cancelled Order List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-note-text</v-icon>
            </v-list-item-icon>
          </v-list-item>

          <v-list-item
            link
            href="/home/order/successed/list"
          >
            <v-list-item-title>Successed Order List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-clipboard-check</v-icon>
            </v-list-item-icon>
          </v-list-item>
        </v-list-group>

        <v-list-group
          prepend-icon="mdi-food"
          value
        >
          <template v-slot:activator>
            <v-list-item-title>Menu</v-list-item-title>
          </template>

          <v-list-item
            link
            href="/home/menu/create"
          >
            <v-list-item-title>Create Menu</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-plus-circle-outline</v-icon>
            </v-list-item-icon>
          </v-list-item>

          <v-list-item
            link
            href="/home/menu/list"
          >
            <v-list-item-title>Menu List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-format-list-bulleted-type</v-icon>
            </v-list-item-icon>
          </v-list-item>
        </v-list-group>

      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      app
      color="blue darken-3"
      dark
      src="/statics/gradient.jpg"
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title
        style="width: 300px"
        class="ml-0 pl-4"
      >
        <span class="hidden-sm-and-down">Kasir</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-menu
        v-model="menu"
        :close-on-content-click="false"
        :nudge-width="200"
        offset-x
      >
        <template v-slot:activator="{ on }">
          <v-btn
            icon
            large
            v-on="on"
          >
            <v-icon>mdi-face</v-icon>
          </v-btn>
        </template>

        <v-card>
          <v-list>
            <v-list-item>
              <v-list-item-avatar>
                <v-icon size="40">mdi-face</v-icon>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title>{{ firstName }} {{ lastName }}</v-list-item-title>
                <v-list-item-subtitle>Logged In</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list>

          <v-divider></v-divider>

          <v-list shaped dense>
            <v-subheader>Account</v-subheader>
            <v-list-item-group color="primary">
              <v-list-item
                v-for="(ip, i) in itemsPopup"
                :key="i"
                :href="ip.url"
              >
                <v-list-item-icon>
                  <v-icon v-text="ip.icon"></v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title v-text="ip.text"></v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>


          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn text @click="menu = false">Close</v-btn>
            <v-btn text @click="logout" color="danger">Logout</v-btn>
          </v-card-actions>
        </v-card>
      </v-menu>
    </v-app-bar>
    <v-main class="blue-grey lighten-5">
      <v-container
        fluid
      >
        <router-view></router-view>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
  import axios from 'axios'
  export default {
    props: {
      source: String,
    },
    data: () => ({
      firstName: null,
      lastName: null,
      fav: true,
      menu: false,
      message: false,
      hints: true,
      dialog: false,
      drawer: true,

      itemPopup: 1,
      itemsPopup: [
        { text: 'Home', icon: 'mdi-view-dashboard', url: '/home' },
        { text: 'Change Identity', icon: 'mdi-account', url: '/home/settings/identity' },
        { text: 'Change Password', icon: 'mdi-account-key', url: '/home/settings/password' },
      ],

      sidebar: [
        { icon: 'mdi-view-dashboard', text: 'Home', link: '/home' },
      ],
    }), // end of data

    methods: {
      logout: function() {
        let currentObj = this
          axios.post('/api/auth/logout')
          .then(function (response) {
            localStorage.removeItem('userToken')
            currentObj.$router.push('/login')
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      getMe: function() {
        let currentObj = this
        axios.get('api/auth/me')
          .then(function (response) {

            currentObj.firstName = response.data.user.first_name || 'FirstName'
            currentObj.lastName = response.data.user.last_name || 'LastName'
          })
          .catch(function (error) {
            if(error.response) {
              console.log(error.response.data.errors)
            }
          })
      }

    }, // End of Methods

    mounted: function() {
      let currentObj = this

      currentObj.getMe()
    }
  }
</script>
