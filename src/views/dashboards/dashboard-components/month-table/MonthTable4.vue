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
      <b-col cols="5">
      </b-col>
      <b-col cols="3" class="text-right">
        <div class="ml-auto mt-2 mt-md-0">
          <div class="btn-grp">
            <b-form-select v-model="selected" :options="options" @change="getAllMembersParticipation"></b-form-select>
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
        v-if="items.filter((element) => element != null).length > 0"
        empty-text="Pas de données"
    >

      <template #cell(image)="data">
<!--        <b-img-->
<!--            :src="'https://mega-tech.dev/jam3iya/back-end/' + data.item.member.image"-->
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
        {{data.item.full_name}}
      </template>

      <template #cell(payed)="data">
        {{getPayedAmount(data.item.years)}}
      </template>

      <template #cell(rest)="data">
        {{currentYear.month_amount - getPayedAmount(data.item.years)}}
      </template>

      <template #cell(month_amount)>
        {{currentYear.month_amount}}
      </template>

      <template #cell(show_details)="data">
        <div class="d-flex align-items-center">
          <b-button
              v-b-popover.hover.left="data.item.full_name"
              size="sm"
              variant="primary"
              @click="hideAlerts(); show2 = true; getItem(data.item); clearForm2()"
              class="mr-2"
              v-b-modal.modal-center2
          >
            Ajouter paiement
          </b-button>

        </div>
      </template>

      <template #cell(show_details2)="row">
        <div class="d-flex align-items-center" v-if="row.item.years.length > 0">

<!--          <b-button-->
<!--                size="sm"-->
<!--                variant="primary"-->
<!--                @click="row.toggleDetails; setTransactions(row.item.years)"-->
<!--                class="mr-2"-->
<!--              >-->
<!--                {{ row.detailsShowing ? "Aficher détails" : "Aficher détails" }} Details-->
<!--              </b-button>-->

          <b-form-checkbox
              v-model="row.detailsShowing"
              @change="setTransactions(row, row.item.years)"
          >
            Aficher détails
          </b-form-checkbox>
        </div>
      </template>

      <template #row-details="row">
        <b-card class="border">
          <b-row>
            <b-col cols="12">
              <div class="px-4">
                <h6 class="mb-3">Détails de paiement de {{row.item.full_name}} pour l'année {{currentYear.name}}</h6>
              </div>
              <div>
                <b-table
                    responsive
                    class="mb-0 mt-3"
                    :items="items2"
                    :fields="fields2"
                    :current-page="currentPage2"
                    :per-page="perPage2"
                    table-class="bg-transparent text-nowrap"
                    v-if="items2.length > 0"
                    empty-text="Pas de données"
                >
                  <template #cell(created_at)="data">
                    <div class="d-flex align-items-center">
                      <div class="ml-3">
                        {{ (data.item.created_at != null) ? data.item.created_at.substr(0, 10).split('-').reverse().join('-').toString() : "" }}
                      </div>
                    </div>
                  </template>
                  <template #cell(show_details3)="data">
                    <div class="d-flex align-items-center">
                      <b-button
                          v-b-popover.hover.left="data.item.motif"
                          size="sm"
                          variant="primary"
                          class="mr-2"
                          @click="deleteTransaction(data.item.id)"
                      >
                        Supprimer
                      </b-button>

                    </div>
                  </template>
                </b-table>
                <div class="d-md-flex align-items-center mt-3 mt-lg-0" v-if="items2.length > 0">
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
                        v-model="perPage2"
                        :options="pageOptions"
                        size="sm"
                    ></b-form-select>
                  </b-form-group>
                  <div class="ml-auto mt-2 mt-md-0">
                    <b-pagination
                        v-model="currentPage2"
                        :total-rows="totalRows2"
                        :per-page="perPage2"
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

              </div>

            </b-col>
          </b-row>
        </b-card>
      </template>

    </b-table>

    <div class="d-md-flex align-items-center mt-3 mt-lg-0" v-if="items.filter((element) => element != null).length > 0">
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
        id="modal-center2"
        hide-footer
        centered
        title="Ajouter un paiement"
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
            <b-form @submit="onSubmit3" v-if="show2">

              <b-row align-content="center" align-v="center">
                <span class="mx-1"></span>
<!--                <b-img-->
<!--                    :src="'https://mega-tech.dev/jam3iya/back-end/' + form.image"-->
<!--                    rounded="circle"-->
<!--                    width="50"-->
<!--                />-->
                <b-img
                    :src="'http://localhost:8000/' + form.image"
                    rounded="circle"
                    width="50"
                />

                <span class="mx-2"></span>
                <h5>{{ form.full_name }}</h5>
              </b-row>
              <br>

              <b-form-group
                  id="input-group-1"
                  label="Motif:"
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form2.motif"
                    type="text"
                    placeholder="Entrez le motif"

                ></b-form-input>
              </b-form-group>

              <b-form-group
                  id="input-group-1"
                  label="Prix:"
                  label-for="input-1"
              >
                <b-form-input
                    id="input-1"
                    v-model="form2.amount"
                    type="number"
                    placeholder="Entrez le prix"
                    required
                ></b-form-input>
              </b-form-group>

              <br>

              <div class="btn-grp">
                <b-button type="submit" variant="primary">Ajouter</b-button>
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
  name: "MonthTable4",
  data: () => ({
    title: "MonthTable4",
    options: [],
    years: [],
    selected: null,
    showError: false,
    showSuccess: false,
    filter: null,
    fields: [
      {
        key: "image",
        label: "Image",
      },
      {
        key: "full_name",
        label: "Nom et Prénom",
      },
      {
        key: "month_amount",
        label: "Montant Annuelle",
      },
      {
        key: "payed",
        label: "Payé",
      },
      {
        key: "rest",
        label: "Reste",
      },
      {
        key: "show_details",
        label: "",
      },
      {
        key: "show_details2",
        label: "",
      },
    ],
    fields2: [
      {
        key: "id",
        label: "#Transaction",
      },
      {
        key: "amount",
        label: "Montant",
      },
      {
        key: "motif",
        label: "Motif",
      },
      {
        key: "created_at",
        label: "Date",
      },
      {
        key: "show_details3",
        label: "",
      },
    ],
    items: [],
    items2: [],
    totalRows: 1,
    totalRows2: 1,
    currentPage: 1,
    currentPage2: 1,
    perPage: 5,
    perPage2: 5,
    pageOptions: [5, 10, 15, { value: 100, text: "Afficher Bcp." }],
    form: {
      year_id: 0,
      id: 0,
      full_name: "",
      image: "",
    },
    form2: {
      year_id: 0,
      id: 0,
      amount: "",
      motif: "",
    },
    show2: true,
    currentYear: null
  }),
  mounted() {
    // Set the initial number of items
    this.totalRows = this.items.filter((element) => element != null).length;
    this.totalRows2 = this.items2.filter((element) => element != null).length;
    this.getAllYears();
  },
  methods: {
    getAllYears() {
      this.$http.get("years/get-all")
          .then(response => {
            if(response.status === 200){
              this.years = response.data.data
              response.data.data.forEach(
                  (element) => this.options.push({
                    value: element.id,
                    text: element.name,
                    disabled: element.is_active == 0,
                  })
              );
              this.selected = response.data.data.filter((element) => element.is_current == 1)[0].id
              this.currentYear = response.data.data.filter((element) => element.is_current == 1)[0]
              this.getAllMembersParticipation()
            }else{
              this.options = []
            }
          })
          .catch(error => {
            console.log(error.response)
          });
    },
    getAllMembersParticipation() {
      this.currentYear = this.years.filter((element) => element.id == this.selected)[0]
      this.$http.get("members-participation/get-all/" + this.selected)
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
    getItem(item) {
      this.form.id = item.id

      this.form.year_id = item.year_id

      this.form.full_name = item.full_name
      this.form.image = item.image

    },
    hideAlerts() {
      this.showError = false
      this.showSuccess = false
    },
    onSubmit2(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false

      this.$http.post("members-participation/update", this.form)
          .then(response => {
            if(response.status === 200){
              this.showSuccess = true
              this.getAllMembersParticipation()
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
    onSubmit3(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false


      this.form2.year_id = this.currentYear.id
      this.form2["member_id"] = this.form.id
      this.$http.post("members-participation/create", this.form2)
          .then(response => {
            if(response.status === 200){
              this.showSuccess = true
              this.getAllMembersParticipation()
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
    getPayedAmount(data) {
      var sum = 0
      data.forEach(item => {
        sum += item.amount;
      });
      return sum
    },
    clearForm2() {
      this.form2.motif = ""
      this.form2.amount = 0
    },
    setTransactions(row, data) {
      row.toggleDetails()
      this.items2 = data
    },
    deleteTransaction(idTransaction) {


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



              this.$http.post("members-participation/delete", {id: idTransaction})
                  .then(response => {
                    if(response.status === 200){
                      this.getAllMembersParticipation()
                    }
                  })
                  .catch(error => {
                    this.error = error.response.data
                    console.log(error)
                  });



            }

          })
          .catch(err => {
            console.log(err)
          })


    }
  },
};
</script>