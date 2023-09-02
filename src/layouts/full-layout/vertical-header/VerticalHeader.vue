<template>
  <header class="topbar">
    <b-navbar
      toggleable="lg"
      :type="`${navbarColor == 'white' ? 'light' : 'dark'}`"
      class="p-0 d-block"
      :variant="navbarColor"
    >



<!--      <div :class="`navbar-header d-block d-lg-none`">-->
<!--        <LogoDark v-if="navbarColor == 'white'" />-->
<!--        <LogoLight v-else />-->
<!--      </div>-->

      <b-row class="d-md-none">
        <b-col cols="9">
          <b-navbar-nav>
            <b-nav-item-dropdown right no-caret>
              <template #button-content>
                <b-row class="d-flex align-items-center py-3">
                  <img
                      src="../../../assets/images/users/1.jpg"
                      alt="user"
                      class="rounded-circle"
                      width="31"
                  />
                  <div class="mx-1"></div>
                  <h4 class="mb-0">{{ full_name }}</h4>
                </b-row>
              </template>
              <b-dropdown-text class="bg-primary text-white text-right">
                <div class="d-flex align-items-center py-2">
                  <b-img
                      src="@/assets/images/users/1.jpg"
                      width="50"
                      rounded="circle"
                  />
                  <div class="mr-3">
                    <h4 class="mb-0">{{ full_name }}</h4>
                    <span class="font-weight-light">{{ username }}</span>
                  </div>
                </div>
              </b-dropdown-text>
              <b-dropdown-item class="d-flex align-items-center text-right" @click="$bvModal.show('modal-basic')">
                <feather type="lock" class="feather-sm ml-2 mb-n1"></feather>
                تغيير كلمة السر
              </b-dropdown-item>
              <b-dropdown-divider></b-dropdown-divider>
              <b-dropdown-item href="#" class="d-flex align-items-center text-right" @click="logout">
                <feather type="log-out" class="feather-sm ml-2 mb-n1"></feather>
                تسجيل الخروج
              </b-dropdown-item>
            </b-nav-item-dropdown>
          </b-navbar-nav>
        </b-col>
        <b-col cols="3">
          <b-navbar-nav class="d-lg-none"
          ><b-nav-item @click="showMobileSidebar" class="d-block d-lg-none"
          ><i class="mdi mdi-menu fs-3"></i></b-nav-item
          ></b-navbar-nav>
        </b-col>
      </b-row>

      <b-row class="bv-d-sm-down-none">
        <b-col md="8" cols="12">
          <b-navbar-nav class="mt-1">
            <b-nav-item @click="setMiniSidebar" class="d-lg-block">
              <i class="mdi mdi-menu fs-3"></i>
            </b-nav-item>
          </b-navbar-nav>
        </b-col>
        <b-col class="text-left float-left" align-self="left" align="left" md="4" cols="12">
          <b-navbar-nav>
            <b-nav-item-dropdown right no-caret>
              <template #button-content>
                <b-row class="d-flex align-items-center text-left py-3">
                  <img
                      src="../../../assets/images/users/1.jpg"
                      alt="user"
                      class="rounded-circle"
                      width="31"
                  />
                  <div class="mx-1"></div>
                  <h4 class="mb-0">{{ full_name }}</h4>
                </b-row>
              </template>
              <b-dropdown-text class="bg-primary text-white text-right">
                <div class="d-flex align-items-center">
                  <b-img
                      src="@/assets/images/users/1.jpg"
                      width="50"
                      rounded="circle"
                  />
                  <div class="mr-3">
                    <h4 class="mb-0">{{ full_name }}</h4>
                    <span class="font-weight-light">{{ username }}</span>
                  </div>
                </div>
              </b-dropdown-text>
              <b-dropdown-item class="d-flex align-items-center text-right" @click="$bvModal.show('modal-basic')">
                <feather type="lock" class="feather-sm ml-2 mb-n1"></feather>
                تغيير كلمة السر
              </b-dropdown-item>
              <b-dropdown-divider></b-dropdown-divider>
              <b-dropdown-item href="#" class="d-flex align-items-center text-right" @click="logout">
                <feather type="log-out" class="feather-sm ml-2 mb-n1"></feather>
                تسجيل الخروج
              </b-dropdown-item>
            </b-nav-item-dropdown>
          </b-navbar-nav>
        </b-col>
      </b-row>
    </b-navbar>

    <b-modal id="modal-basic" hide-footer>
      <template #modal-title>تغيير كلمة المرور</template>
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
                placeholder="ادخل كلمة المرور الجديدة"
                required
                type="password"
            ></b-form-input>
          </b-form-group>
        </b-form>
      </div>
      <div class="pt-3 border-top text-right">
        <b-button variant="primary" @click="updatePassword()" >تعديل</b-button>
      </div>
    </b-modal>
  </header>
</template>

<script>
import { mapState } from "vuex";
// import LogoDark from "../logo-dark/LogoDark";
// import LogoLight from "../logo-light/LogoLight";
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
    newPassword: null,
    drawerOpened: false
  }),
  components: {
    // LogoDark,
    // LogoLight,
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
    this.$store.commit("SET_SIDEBAR_DRAWER", this.drawerOpened);
    this.getCurrentUser()
  },
  methods: {
    showMobileSidebar() {
      this.$store.commit("SET_SIDEBAR_DRAWER", !this.drawerOpened);
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
