<template>
  <header class="topbar">
    <b-navbar
      toggleable="lg"
      :type="`${navbarColor == 'white' ? 'light' : 'dark'}`"
      class="p-0"
      :variant="navbarColor"
    >
      <b-navbar-nav
        ><b-nav-item @click="showMobileSidebar" class="d-block d-lg-none"
          ><i class="mdi mdi-menu fs-3"></i></b-nav-item
      ></b-navbar-nav>
      <div :class="`navbar-header d-block d-lg-none`">
        <LogoDark v-if="navbarColor == 'white'" />
        <LogoLight v-else />
      </div>
      <b-navbar-toggle target="nav-collapse" class="mx-2 border-0"
        ><i class="ti-more"></i
      ></b-navbar-toggle>
      <b-collapse id="nav-collapse" is-nav class="">
        <b-navbar-nav>
          <b-nav-item @click="setMiniSidebar" class="d-none d-lg-block"
            ><i class="mdi mdi-menu fs-3"></i
          ></b-nav-item>

        </b-navbar-nav>

        <b-navbar-nav class="ml-auto">

          <b-nav-item-dropdown right no-caret class="mailbox">
            <!-- Using 'button-content' slot -->
            <template #button-content>
              <b-row class="d-flex align-items-center py-3">
                <img
                    src="../../../assets/images/users/1.jpg"
                    alt="user"
                    class="rounded-circle"
                    width="31"
                />
                <div class="mx-2"></div>
                <h4 class="mb-0">{{ full_name }}</h4>
                <div class="mx-2"></div>
              </b-row>
            </template>
            <b-dropdown-text class="bg-primary text-white">
              <div class="d-flex align-items-center py-2">
                <b-img
                  src="@/assets/images/users/1.jpg"
                  width="50"
                  rounded="circle"
                />
                <div class="ml-3">
                  <h4 class="mb-0">{{ full_name }}</h4>
                  <span class="font-weight-light">{{ username }}</span>
                </div>
              </div>
            </b-dropdown-text>
            <b-dropdown-item class="d-flex align-items-center" @click="$bvModal.show('modal-basic')">
              <feather type="lock" class="feather-sm mr-2 mb-n1"></feather>
              Changer le mot de passe
            </b-dropdown-item>
            <b-dropdown-divider></b-dropdown-divider>
            <b-dropdown-item href="#" class="d-flex align-items-center" @click="logout">
              <feather type="log-out" class="feather-sm mr-2 mb-n1"></feather>
              Se déconnecter
            </b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>

    <b-modal id="modal-basic" hide-footer>
      <template #modal-title>Changement de Mot de Passe</template>
      <div class="d-block">
        <b-form>
          <b-form-group
              id="input-group-1"
              label=""
              label-for="input-1"
          >
            <b-form-input
                id="input-1"
                v-model="newPassword"
                placeholder="Entrez le nouveau mot de passe"
                required
                type="password"
            ></b-form-input>
          </b-form-group>
        </b-form>
      </div>
      <div class="pt-3 border-top text-right">
        <b-button variant="primary" @click="updatePassword()" >Modifier</b-button>
      </div>
    </b-modal>
  </header>
</template>

<script>
import { mapState } from "vuex";
import LogoDark from "../logo-dark/LogoDark";
import LogoLight from "../logo-light/LogoLight";
//import VuePerfectScrollbar from "vue-perfect-scrollbar";

import img1 from "../../../assets/images/users/1.jpg";
import img2 from "../../../assets/images/users/2.jpg";
import img3 from "../../../assets/images/users/3.jpg";
import img4 from "../../../assets/images/users/4.jpg";

export default {
  name: "vertical-header",
  data: () => ({
    showToggle: false,
    notifications: [
      {
        btnbg: "danger",
        btnicon: "user",
        title: "Launch Admin",
        subtext: "Just see the my new admin!",
        time: "9:30AM",
      },
      {
        btnbg: "success",
        btnicon: "calendar",
        title: "Event today",
        subtext: "Just a reminder that you have event",
        time: "1:30PM",
      },
      {
        btnbg: "primary",
        btnicon: "settings",
        title: "Settings",
        subtext: "You can customize this template as you want",
        time: "9:30AM",
      },
      {
        btnbg: "info",
        btnicon: "bell",
        title: "New Message",
        subtext: "Sent you an notification",
        time: "1:30PM",
      },
    ],
    messages: [
      {
        btnbg: img1,
        badgebg: "danger",
        title: "Launch Admin",
        subtext: "Just see the my new admin!",
        time: "9:30AM",
      },
      {
        btnbg: img2,
        badgebg: "success",
        title: "Event today",
        subtext: "Just a reminder that you have event",
        time: "1:30PM",
      },
      {
        btnbg: img3,
        badgebg: "primary",
        title: "Settings",
        subtext: "You can customize this template as you want",
        time: "9:30AM",
      },
      {
        btnbg: img4,
        badgebg: "warning",
        title: "New Message",
        subtext: "Sent you an notification",
        time: "1:30PM",
      },
    ],
    href() {
      return undefined;
    },
    username: "",
    full_name: "",
    newPassword: null
  }),
  components: {
    LogoDark,
    LogoLight,
    //VuePerfectScrollbar,
  },
  computed: {
    ...mapState(["navbarColor", "logoColor", "LayoutType"]),
    getCurrentLanguage() {
      const locale = this.$i18n.locale;
      if (locale == "en") return { lang: "flag-icon flag-icon-us" };
      else if (locale == "fr") return { lang: "flag-icon flag-icon-fr" };
      else if (locale == "ch") return { lang: "flag-icon flag-icon-ch" };
      else if (locale == "gr") return { lang: "flag-icon flag-icon-gr" };
      return this.locale;
    },
  },
  mounted() {
    this.getCurrentUser()
  },
  methods: {
    showMobileSidebar() {
      this.$store.commit("SET_SIDEBAR_DRAWER", true);
    },
    setMiniSidebar() {
      if (this.LayoutType == "full-sidebar") {
        this.$store.dispatch("setLayoutType", "mini-sidebar");
      } else if (this.LayoutType == "mini-sidebar") {
        this.$store.dispatch("setLayoutType", "full-sidebar");
      }
    },
    changeLanguage(locale) {
      this.$i18n.locale = locale;
    },
    getCurrentUser(){
      const  token  = localStorage.getItem('login');
      this.$http.post("admin/get-my-info",{login: token})
          .then(response => {
            if(response.status === 200){
              if(response.data.success == true){
                this.full_name = response.data.admin.first_name + " " + response.data.admin.last_name
                this.username = response.data.admin.phone
              }
            }else{
              this.full_name = "Admin"
              this.username = "admin@admin.com"
            }
          })
          .catch(error => {
            console.log(error)
          });
    },
    updatePassword() {
      if(this.newPassword.length >= 6) {
        const token = localStorage.getItem('login');
        this.$http.post("admin/update-password", {
          login: token,
          password: this.newPassword
        })
            .then(response => {
              if (response.status === 200) {
                if (response.data.success == true) {
                  this.logout()
                }
              }
            })
            .catch(error => {
              console.log(error)
            });
        this.$bvModal.hide('modal-basic')
      }
    },
    logout(){
      localStorage.removeItem("login")
      localStorage.removeItem("_utoken")
      this.$router.push({ path: "/" });
    }
  },
};
</script>
