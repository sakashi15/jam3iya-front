<template>
  <div>
    <b-row>
      <b-col cols="4">
        <b-form-group
            label-for="filter-input"
            label-align-sm="right"
            class="mb-2"
        >
          <b-input-group>
            <b-form-input
                id="filter-input"
                v-model="filter"
                type="search"
                placeholder="Tapez quelque chose"
            ></b-form-input>

            <b-input-group-append>
              <b-button
                  variant="danger"
                  :disabled="!filter"
                  @click="filter = ''"
              >Vider</b-button
              >
            </b-input-group-append>
          </b-input-group>
        </b-form-group>
      </b-col>
      <b-col cols="4">
      </b-col>
      <b-col cols="4" class="text-right">
        <div class="ml-auto mt-2 mt-md-0">
          <div class="btn-grp">
            <b-button variant="outline-info" v-b-modal.modal-center @click="hideAlerts(); resetAdd()">
              Ajouter personne
            </b-button>
          </div>
        </div>
      </b-col>
    </b-row>



    <b-table
      responsive
      class="mb-0 mt-3"
      :items="items"
      :fields="fields"
      :filter="filter"
      :current-page="currentPage"
      :per-page="perPage"
      table-class="bg-transparent text-nowrap"
      v-if="items.length > 0"
      empty-text="Pas de données"
    >

      <template #cell(image)="data">
<!--        <b-img-->
<!--            :src="'https://mega-tech.dev/jam3iya/back-end/' + data.item.image"-->
<!--            rounded="circle"-->
<!--            width="50"-->
<!--            height="50"-->
<!--            v-if="data.item.image != null"-->
<!--        />-->
        <b-img
            :src="'http://localhost:8000/' + data.item.image"
            rounded="circle"
            width="50"
            height="50"
            v-if="data.item.image != null"
        />
        <b-img
            :src="require('@/assets/images/users/1.jpg')"
            rounded="circle"
            width="50"
            v-else
        />
      </template>
      <template #cell(full_name)="data">
        <div class="d-flex align-items-center">
          <div class="ml-3">
            <h6 class="fw-medium mb-0">
              {{ data.item.full_name }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(email)="data">
        <div class="d-flex align-items-center">
          <div class="ml-3">
            <h6 class="fw-medium mb-0">
              {{ data.item.email }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(phone)="data">
        <div class="d-flex align-items-center">
          <div class="ml-3">
            <h6 class="fw-medium mb-0">
              {{ data.item.phone }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(birth_date)="data">
        <div class="d-flex align-items-center">
          <div class="ml-3">
            <h6 class="fw-medium mb-0">
              {{ (data.item.birth_date != null) ? data.item.birth_date.split('-').reverse().join('-').toString() : "" }}
            </h6>
          </div>
        </div>
      </template>

      <template #cell(show_details)="data">
        <div class="d-flex align-items-center">
          <b-button
            v-b-popover.hover.left="data.item.full_name"
            title="Nom complète"
            size="sm"
            variant="primary"
            @click="hideAlerts(); show2 = true; getItem(data.item)"
            class="mr-2"
            v-b-modal.modal-center2
          >
             Modifier / Supprimer
          </b-button>

        </div>
      </template>

    </b-table>

    <div class="d-md-flex align-items-center mt-3 mt-lg-0" v-if="items.length > 0">
      <b-form-group
        label="Par page"
        label-for="per-page-select"
        label-cols-sm="6"
        label-cols-md="5"
        label-cols-lg="9"
        label-size="sm"
        label-class="fw-medium"
        class="mb-0"
      >
        <b-form-select
          id="per-page-select"
          v-model="perPage"
          :options="pageOptions"
          size="sm"
        ></b-form-select>
      </b-form-group>
      <div class="ml-auto mt-2 mt-md-0">
        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
          align="fill"
          class="my-0"
        ></b-pagination>
      </div>
    </div>

    <center v-else>
      <br/>
      <br/>
      <b-img :src="require('@/assets/images/folder.png')" width="150" height="150"/>
      <br/>
      <br/>
      <h5>Pas de données</h5>
    </center>

    <b-modal
        id="modal-center"
        hide-footer
        centered
        title="Ajouter une personne"
    >
      <div class="d-block">
        <b-row>
          <b-col cols="12" lg="12">
            <b-alert
                variant="success"
                class="d-flex align-items-center bt-alert"
                show
                dismissible
                fade
                v-if="showSuccess"
            >
              <b-button variant="success">
                <i class="mdi mdi-alert-octagon"></i>
              </b-button>
              Opération réussie
            </b-alert>
            <b-alert
                variant="danger"
                class="d-flex align-items-center bt-alert"
                show
                dismissible
                fade
                v-if="showError"
            >
              <b-button variant="danger">
                <i class="mdi mdi-alert-octagon"></i>
              </b-button>
              <b-col>
                <div v-for="(errorArray, idx) in error.errors" :key="idx">
                  <div v-for="(allErrors, idx) in errorArray" :key="idx">
                    <span class="text-danger">{{ allErrors}} </span>
                  </div>
                </div>
              </b-col>
            </b-alert>
            <b-form @submit="onSubmit" @reset="onReset" v-if="show">
              <b-form-group
                  id="input-group-1"
                  label="Adress email:"
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.email"
                    type="email"
                    placeholder="Entrez l'e-mail"
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Le nom et le prénom:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    v-model="form.full_name"
                    placeholder="Entez nom et prénom"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="La date de naissance:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    type="date"
                    v-model="form.birth_date"
                    placeholder="Entez la date de naissance"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Téléphone:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    v-model="form.phone"
                    placeholder="Entez la numéro téléphone"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Image:"
                  label-for="input-2"
              >
                <b-form-file
                    id="file-default"
                    type="file"
                    v-model="form.image"
                    placeholder="selectionnez une image"
                ></b-form-file>
              </b-form-group>





              <div class="btn-grp">
                <b-button type="submit" variant="primary">Ajouter</b-button>
                <b-button type="reset" variant="outline-danger">Vider</b-button>
              </div>
            </b-form>
          </b-col>
        </b-row>
      </div>
    </b-modal>


    <b-modal
        id="modal-center2"
        hide-footer
        centered
        title="Modifier une personne"
    >
      <div class="d-block">
        <b-row>
          <b-col cols="12" lg="12">
            <b-alert
                variant="success"
                class="d-flex align-items-center bt-alert"
                show
                dismissible
                fade
                v-if="showSuccess"
            >
              <b-button variant="success">
                <i class="mdi mdi-alert-octagon"></i>
              </b-button>
              Opération réussie
            </b-alert>
            <b-alert
                variant="danger"
                class="d-flex align-items-center bt-alert"
                show
                dismissible
                fade
                v-if="showError"
            >
              <b-button variant="danger">
                <i class="mdi mdi-alert-octagon"></i>
              </b-button>
              <b-col>
                <div v-for="(errorArray, idx) in error.errors" :key="idx">
                  <div v-for="(allErrors, idx) in errorArray" :key="idx">
                    <span class="text-danger">{{ allErrors}} </span>
                  </div>
                </div>
              </b-col>
            </b-alert>
            <b-form @submit="onSubmit2" v-if="show2">
              <b-form-group
                  id="input-group-1"
                  label="Adress email:"
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.email"
                    type="email"
                    placeholder="Entrez l'e-mail"
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Le nom et le prénom:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    v-model="form.full_name"
                    placeholder="Entez nom et prénom"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="La date de naissance:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    type="date"
                    v-model="form.birth_date"
                    placeholder="Entez la date de naissance"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Téléphone:"
                  label-for="input-2"
              >
                <b-form-input
                    id="input-2"
                    v-model="form.phone"
                    placeholder="Entez la numéro téléphone"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-2"
                  label="Image:"
                  label-for="input-2"
              >
                <b-form-file
                    id="file-default"
                    type="file"
                    v-model="currentImage"
                    placeholder="selectionnez une image"
                ></b-form-file>
              </b-form-group>





              <div class="btn-grp">
                <b-button type="submit" variant="primary">Modifier</b-button>
                <b-button @click="onReset2" variant="outline-danger">Supprimer</b-button>
              </div>
            </b-form>
          </b-col>
        </b-row>
      </div>
    </b-modal>


  </div>



</template>
<script>

export default {
  name: "MonthTable",
  data: () => ({
    title: "MonthTable",
    filter: null,
    fields: [
      {
        key: "image",
        label: "Image",
      },
      {
        key: "full_name",
        label: "Nom et prénom",
      },
      {
        key: "email",
        label: "Email",
      },
      {
        key: "phone",
        label: "Téléphone",
      },
      {
        key: "birth_date",
        label: "Date Naissance",
      },
      {
        key: "show_details",
        label: "",
      },

    ],
    items: [],
    totalRows: 1,
    currentPage: 1,
    perPage: 5,
    pageOptions: [5, 10, 15, { value: 100, text: "Afficher Bcp." }],
    form: {
      email: "",
      full_name: "",
      birth_date: "",
      phone: "",
      image: null
    },
    show: true,
    show2: true,
    error: null,
    showError: false,
    showSuccess: false,
    currentImage: null
  }),
  mounted() {
    this.totalRows = this.items.length;
    this.getAllPersons()
  },
  methods: {
    onSubmit(event) {
      event.preventDefault();
      this.showError = false
      this.showSuccess = false
      this.$http.post("members/create", this.form, {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      })
          .then(response => {
            if(response.status === 200){
              this.showSuccess = true
              this.getAllPersons()
              this.onReset()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.showSuccess = false
            this.error = error.response.data
            console.log(error)
            this.showError = true
          });
    },
    getAllPersons() {
      this.$http.get("members/get-all")
          .then(response => {
            if(response.status === 200){
              this.items = response.data.data
            }else{
              this.items = []
            }
            console.log(response.data.data)
          })
          .catch(error => {
            console.log(error.response.data)
          });
    },
    onSubmit2(event) {
      event.preventDefault();
      this.showError = false
      this.showSuccess = false
      if(this.currentImage !== null){
        this.form.image = this.currentImage;
      }else{
        delete this.form['image'];
      }
      this.currentImage = null
      this.$http.post("members/update", this.form, {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      })
          .then(response => {
            if(response.status === 200){
              this.showSuccess = true
              this.getAllPersons()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.showSuccess = false
            this.error = error.response.data
            console.log(error)
            this.showError = true
          });
    },
    onReset() {
   //   event.preventDefault();
      this.form.email = "";
      this.form.full_name = "";
      this.form.birth_date = "";
      this.form.phone = "";
      this.form.image = null;
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    },
    onReset2() {

      this.$bvModal.msgBoxConfirm('Veuillez confirmer que vous souhaitez supprimer.', {
        title: 'Veuillez confirmer',
        size: 'sm',
        buttonSize: 'sm',
        okVariant: 'danger',
        okTitle: 'Oui',
        cancelTitle: 'Non',
        footerClass: 'p-2',
        hideHeaderClose: false,
        centered: true
      })
          .then(value => {

            if(value === true) {



              this.showError = false
              this.currentImage = null
              this.$http.post("members/delete", this.form)
                  .then(response => {
                    if(response.status === 200){
                      this.show2 = false
                      this.showSuccess = true
                      this.getAllPersons()
                    }else{
                      this.showError = true
                    }
                  })
                  .catch(error => {
                    this.error = error.response.data
                    console.log(error)
                    this.showError = true
                  });



            }

          })
          .catch(err => {
            console.log(err)
          })




    },
    getItem(item) {
      this.hideAlerts()

      this.form.id = item.id
      this.form.image = item.image
      this.form.phone = item.phone
      this.form.full_name = item.full_name
      this.form.birth_date = item.birth_date
      this.form.email = item.email
    },
    resetAdd() {
      this.form.email = "";
      this.form.full_name = "";
      this.form.birth_date = "";
      this.form.phone = "";
      this.form.image = null;
    },
    hideAlerts() {
      this.showError = false
      this.showSuccess = false
    }
  },
};
</script>