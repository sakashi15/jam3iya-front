<template>
  <div>
    <b-row>
      <b-col cols="6">
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
      <b-col cols="2">
      </b-col>
      <b-col cols="4" class="text-right">
        <div class="ml-auto mt-2 mt-md-0">
          <div class="btn-grp">
            <b-button variant="outline-info" v-b-modal.modal-center @click="hideAlerts(); resetAdd()">
              Ajouter année
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

      <template #cell(is_active)="data">
        <div class="d-flex align-items-center">
          {{(data.item.is_active == true) ? 'Oui' : "Non"}}
        </div>
      </template>

      <template #cell(is_current)="data">
        <div class="d-flex align-items-center">
          {{(data.item.is_current == true) ? 'Oui' : "Non"}}
        </div>
      </template>

      <template #cell(show_details)="data">
        <div class="d-flex align-items-center">
          <b-button
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
      <b-spinner v-if="isLoading" label="Loading..."></b-spinner>
      <div v-else>
        <br/>
        <br/>
        <b-img :src="require('@/assets/images/folder.png')" width="150" height="150"/>
        <br/>
        <br/>
        <h5>Pas de données</h5>
      </div>
    </center>

    <b-modal
        id="modal-center"
        hide-footer
        centered
        title="Ajouter une année"
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
                  label="Année: "
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.name"
                    placeholder="Entrez l'année"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-1"
                  label="Prix mensuelle: "
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.month_amount"
                    type="number"
                      placeholder="Entrez le prix annuelle"
                    required
                ></b-form-input>
              </b-form-group>

              <b-row>
                <b-col>
                  <b-form-group
                      id="input-group-1"
                      label-for="input-1"
                  >
                    <b-form-checkbox
                        id="checkbox-1"
                        v-model="form.is_active"
                        :unchecked-value="false"
                        name="checkbox-1"
                    >
                      Actif
                    </b-form-checkbox>
                  </b-form-group>
                </b-col>
                <b-col>
                  <b-form-group
                      id="input-group-1"
                      label-for="input-1"
                  >
                    <b-form-checkbox
                        id="checkbox-2"
                        v-model="form.is_current"
                        :unchecked-value="false"
                        name="checkbox-2"
                    >
                      Courante
                    </b-form-checkbox>
                  </b-form-group>
                </b-col>
              </b-row>

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
        title="Modifier une année"
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
                  label="Année: "
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.name"
                    placeholder="Entrez l'année"
                    required
                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-1"
                  label="Prix mensuelle: "
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form.month_amount"
                    type="number"
                    placeholder="Entrez le prix mensuelle"
                    required
                ></b-form-input>
              </b-form-group>

              <b-row>
                <b-col>
                  <b-form-group
                      id="input-group-1"
                      label-for="input-1"
                  >
                    <b-form-checkbox
                        id="checkbox-2"
                        v-model="form.is_active"
                        :unchecked-value="false"
                        name="checkbox-2"
                    >
                      Actif
                    </b-form-checkbox>
                  </b-form-group>
                </b-col>
                <b-col>
                  <b-form-group
                      id="input-group-1"
                      label-for="input-1"
                  >
                    <b-form-checkbox
                        id="checkbox-3"
                        v-model="form.is_current"
                        :unchecked-value="false"
                        name="checkbox-3"
                    >
                      Courante
                    </b-form-checkbox>
                  </b-form-group>
                </b-col>
              </b-row>

              <div class="btn-grp">
                <b-button type="submit" variant="primary">Modifier</b-button>
                <b-button variant="outline-danger" @click="onReset2">Supprimer</b-button>
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
  name: "MonthTable5",
  data: () => ({
    title: "MonthTable5",
    filter: null,
    fields: [
      {
        key: "name",
        label: "Nom",
      },
      {
        key: "month_amount",
        label: "Prix Annuelle (DA)",
      },
      {
        key: "is_active",
        label: "Activé",
      },
      {
        key: "is_current",
        label: "Actuelle",
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
      id: 0,
      name : "",
      month_amount : "",
      is_active : false,
      is_current : false,
    },
    show: true,
    show2: true,
    error: null,
    showError: false,
    showSuccess: false,
    isLoading: false
  }),
  mounted() {
    this.totalRows = this.items.length;
    this.getAllYears()
  },
  methods: {
    onSubmit(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false
      this.isLoading = true
      this.$http.post("years/create", this.form)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.resetAdd()
              this.getAllYears()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.isLoading = false
            this.showSuccess = false
            this.error = error.response.data
            console.log(error)
            this.showError = true
          });
    },
    getAllYears() {
      this.isLoading = true
      this.$http.get("years/get-all")
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              response.data.data.forEach((item) => {
                item.is_active = item.is_active == 1
                item.is_current = item.is_current == 1
              })
              this.items = response.data.data
            }else{
              this.items = []
            }
            console.log(response.data.data)
          })
          .catch(error => {
            this.isLoading = false
            console.log(error.response.data)
          });
    },
    onSubmit2(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false
      this.isLoading = true
      this.$http.post("years/update", this.form)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.getAllYears()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.isLoading = false
            this.showSuccess = false
            this.error = error.response.data
            console.log(error)
            this.showError = true
          });

    },
    onReset(event) {
      event.preventDefault();
      this.form.id = 0;
      this.form.name = "";
      this.form.month_amount = 0;
      this.form.is_active = false;
      this.form.is_current = false;

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


              this.isLoading = true
              this.showError = false
              this.$http.post("years/delete", this.form)
                  .then(response => {
                    this.isLoading = false
                    if(response.status === 200){
                      this.show2 = false
                      this.showSuccess = true
                      this.getAllYears()
                    }else{
                      this.showError = true
                    }
                  })
                  .catch(error => {
                    this.isLoading = false
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

      this.form.id = item.id;
      this.form.name = item.name;
      this.form.month_amount = item.month_amount;
      this.form.is_active = item.is_active;
      this.form.is_current = item.is_current;
    },
    resetAdd() {
      this.form.id = 0;
      this.form.name = "";
      this.form.month_amount = 0;
      this.form.is_active = false;
      this.form.is_current = false;
    },
    hideAlerts() {
      this.showError = false
      this.showSuccess = false
    }
  },
};
</script>