<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      :clipped="$vuetify.breakpoint.lgAndUp"
      app
    >
      <v-list>
        <v-list-item
          link
          href="/siAdmino"
        >
          <v-list-item-icon>
            <v-icon>mdi-view-dashboard-outline</v-icon>
          </v-list-item-icon>
          <v-list-item-title>Home Page</v-list-item-title>
        </v-list-item>

        <v-list-group
          prepend-icon="mdi-account-multiple"
          value
        >
          <template v-slot:activator>
            <v-list-item-title>Users</v-list-item-title>
          </template>

          <v-list-item
            link
            color="red"
            href="/siAdmino/users/create"
          >
            <v-list-item-title>Create User</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-account-plus</v-icon>
            </v-list-item-icon>
          </v-list-item>
          <v-list-item
            link
            color="red"
            href="/siAdmino/users/cashier/list"
          >
            <v-list-item-title>Cashier Users List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-account</v-icon>
            </v-list-item-icon>
          </v-list-item>
          <v-list-item
            link
            color="red"
            href="/siAdmino/users/admin/list"
          >
            <v-list-item-title>Admin Users List</v-list-item-title>
            <v-list-item-icon>
              <v-icon>mdi-account</v-icon>
            </v-list-item-icon>
          </v-list-item>
        </v-list-group>

      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      app
      color="red lighten-1"
      dark
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title
        style="width: 300px"
        class="ml-0 pl-4"
      >
        <span class="hidden-sm-and-down">Kasir <code class="red--text text--lighten-1">Admin</code></span>
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
    <v-main>
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
      drawer: null,

      itemPopup: 1,
      itemsPopup: [
        { text: 'Home', icon: 'mdi-view-dashboard', url: '/siAdmino' },
        { text: 'Change Identity', icon: 'mdi-account', url: '/siAdmino/settings/identity' },
        { text: 'Change Password', icon: 'mdi-account-key', url: '/siAdmino/settings/password' },
      ],

      sidebar: [
        { icon: 'mdi-view-dashboard', text: 'Home', link: '/' },
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