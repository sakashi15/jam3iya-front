<template>
  <div>
    <div v-if="hasAccess">
      <div>

        <b-row>
          <b-col cols="12" md="4">
            <b-form-group
                label-for="filter-input"
                label-align-sm="right"
                class="mb-2"
            >
              <b-input-group>


                <b-input-group-append>
                  <b-button
                      variant="danger"
                      :disabled="!filter"
                      @click="filter = ''"
                  >تفريغ</b-button
                  >
                </b-input-group-append>
                <b-form-input
                    id="filter-input"
                    v-model="filter"
                    type="search"
                    placeholder="اكتب شيء للبحث"
                ></b-form-input>
              </b-input-group>
            </b-form-group>
          </b-col>
          <b-col md="4" cols="12">
          </b-col>
          <b-col md="4" cols="12" class="text-left">
            <div class="ml-auto mt-2 mt-md-0">
              <div class="btn-grp">
                <b-form-select v-model="selected" :options="options" @change="getAllMembersParticipation"></b-form-select>
              </div>
            </div>
          </b-col>
        </b-row>

        <b-table
            responsive
            class="mb-0 mt-3 text-right"
            :items="items"
            :fields="fields"
            :filter="filter"
            :current-page="currentPage"
            :per-page="perPage"
            table-class="bg-transparent text-nowrap"
            v-if="items.filter((element) => element != null).length > 0"
            empty-text="لايوجد بيانات"
        >

          <template #cell(image)="data">
                    <b-img
                        :src="'https://mega-tech.dev/jam3iya/back-end/' + data.item.image"
                        rounded="circle"
                        width="50"
                        height="50"
                        v-if="data.item.image != null"
                    />
<!--            <b-img-->
<!--                :src="'http://localhost:8000/' + data.item.image"-->
<!--                rounded="circle"-->
<!--                width="50"-->
<!--                height="50"-->
<!--                v-if="data.item.image != null"-->
<!--            />-->
            <b-img
                :src="require('@/assets/images/users/1.jpg')"
                rounded="circle"
                width="50"
                height="50"
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
            {{parseFloat(currentYear.month_amount) - getPayedAmount(data.item.years)}}
          </template>

          <template #cell(month_amount)>
            {{parseFloat(currentYear.month_amount)}}
          </template>

          <template #cell(show_details)="data">
            <div>
              <b-button
                  v-b-popover.hover.left="data.item.full_name"
                  size="sm"
                  variant="primary"
                  @click="hideAlerts(); show2 = true; getItem(data.item); clearForm2()"
                  class="mr-2"
                  v-b-modal.modal-center2
              >
                اضافة دفعة
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
                تفاصيل الدفع
              </b-form-checkbox>
            </div>
          </template>

          <template #row-details="row">
            <b-card class="border" style="background-color: #EEF5F9">
              <b-row>
                <b-col cols="12">
                  <div class="px-4">
                    <h6 class="mb-3">تفاصيل الدفع للسيد: {{row.item.full_name}}، لسنة {{currentYear.name}}</h6>
                  </div>
                  <div>
                    <b-table
                        responsive
                        class="mb-0 mt-3 text-right"
                        :items="items2"
                        :fields="fields2"
                        :current-page="currentPage2"
                        :per-page="perPage2"
                        table-class="bg-transparent text-nowrap"
                        v-if="items2.length > 0"
                        empty-text="لايوجد بيانات"
                    >
                      <template #cell(created_at)="data">
                        <div>
                          <div>
                            {{ (data.item.created_at != null) ? data.item.created_at.substr(0, 10).toString() : "" }}
                          </div>
                        </div>
                      </template>
                      <template #cell(show_details3)="data">
                        <div>
                          <b-button
                              v-b-popover.hover.left="data.item.motif"
                              size="sm"
                              variant="primary"
                              class="mr-2"
                              @click="deleteTransaction(data.item.id)"
                          >
                            حذف
                          </b-button>

                        </div>
                      </template>
                    </b-table>
                    <div v-if="items2.length > 0">
                      <b-pagination
                          class="mt-3 float-left"
                          v-model="currentPage2"
                          :total-rows="totalRows2"
                          :per-page="perPage2"
                      ></b-pagination>
                    </div>
                    <center v-else>
                      <br/>
                      <br/>
                      <b-img :src="require('@/assets/images/folder.png')" width="150" height="150"/>
                      <br/>
                      <br/>
                      <h5>لايوجد بيانات</h5>
                    </center>

                  </div>

                </b-col>
              </b-row>
            </b-card>
          </template>

        </b-table>

        <div v-if="items.filter((element) => element != null).length > 0">
          <b-pagination
              class="mt-3 float-left"
              v-model="currentPage"
              :total-rows="totalRows"
              :per-page="perPage"
          ></b-pagination>
        </div>

        <center v-else>
          <b-spinner v-if="isLoading" label="Loading..."></b-spinner>
          <div v-else>
            <br/>
            <br/>
            <b-img :src="require('@/assets/images/folder.png')" width="150" height="150"/>
            <br/>
            <br/>
            <h5>لايوجد بيانات</h5>
          </div>
        </center>

        <b-modal
            id="modal-center2"
            hide-footer
            centered
            title="اضافة دفعة"
        >
          <div class="d-block">
            <b-overlay :show="isLoading" rounded="sm">
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
                  <span class="mr-4 text-right">تمت العملية بنجاح</span>
                </b-alert>
                <b-alert
                    variant="danger"
                    class="d-flex align-items-center bt-alert text-right"
                    show
                    dismissible
                    fade
                    v-if="showError"
                >
                  <b-col class="mr-4">
                    <div v-for="(errorArray, idx) in error.errors" :key="idx">
                      <div v-for="(allErrors, idx) in errorArray" :key="idx">
                        <span class="text-danger text-right">{{ allErrors}} </span>
                      </div>
                    </div>
                    <div v-if="showErrorInvalid">
                      <span class="text-danger text-right">{{ error.message }} </span>
                    </div>
                  </b-col>
                </b-alert>
                <b-form @submit="onSubmit3" v-if="show2">

                  <b-row align-content="center" align-v="center">
                    <span class="mx-1"></span>
                                    <b-img
                                        :src="'https://mega-tech.dev/jam3iya/back-end/' + form.image"
                                        rounded="circle"
                                        width="50"
                                        height="50"
                                        v-if="form.image != null"
                                    />
                    <b-img
                        :src="require('@/assets/images/users/1.jpg')"
                        rounded="circle"
                        width="50"
                        height="50"
                        v-else
                    />
<!--                    <b-img-->
<!--                        :src="'http://localhost:8000/' + form.image"-->
<!--                        rounded="circle"-->
<!--                        width="50"-->
<!--                    />-->

                    <span class="mx-2"></span>
                    <h5>{{ form.full_name }}</h5>
                  </b-row>
                  <br>

                  <b-form-group
                      id="input-group-1"
                      label="النمط:"
                      label-for="input-1"
                      class="text-right"
                  >
                    <b-form-input
                        id="input-1"
                        v-model="form2.motif"
                        type="text"
                        placeholder="ادخل النمط"

                    ></b-form-input>
                  </b-form-group>

                  <b-form-group
                      id="input-group-1"
                      label="المبلغ:"
                      label-for="input-1"
                      class="text-right"
                  >
                    <b-form-input
                        id="input-1"
                        v-model="form2.amount"
                        type="number"
                        placeholder="ادخل المبلغ"
                        required
                    ></b-form-input>
                  </b-form-group>

                  <br>

                  <div class="btn-grp">
                    <b-button type="submit" variant="primary">اضافة</b-button>
                  </div>
                </b-form>
              </b-col>
            </b-row>
            </b-overlay>
          </div>
        </b-modal>

      </div>
    </div>
    <div v-else>
      <center>
        <br/>
        <br/>
        <b-img :src="require('@/assets/images/folder.png')" width="150" height="150"/>
      </center>
    </div>
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
        label: "الصورة",
      },
      {
        key: "full_name",
        label: "الاسم واللقب",
      },
      {
        key: "month_amount",
        label: "المبلغ السنوي",
      },
      {
        key: "payed",
        label: "المبلغ المدفوع",
      },
      {
        key: "rest",
        label: "المبلغ الباقي",
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
        label: "#الدفعة",
      },
      {
        key: "amount",
        label: "المبلغ",
      },
      {
        key: "motif",
        label: "النمط",
      },
      {
        key: "created_at",
        label: "التاريخ",
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
    pageOptions: [5, 10, 15, { value: 100, text: "اضهر اكثر" }],
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
    currentYear: null,
    hasAccess: false,
    showErrorInvalid: false,
    isLoading: false
  }),
  mounted() {
    var role = localStorage.getItem("role");
    this.hasAccess = role === "admin"

    // Set the initial number of items
    this.totalRows = this.items.filter((element) => element != null).length;
    this.totalRows2 = this.items2.filter((element) => element != null).length;
    this.getAllYears();
  },
  methods: {
    getAllYears() {
      this.isLoading = true
      this.$http.get("years/get-all")
          .then(response => {
            this.isLoading = false
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
            this.isLoading = false
            console.log(error.response)
          });
    },
    getAllMembersParticipation() {
      this.currentYear = this.years.filter((element) => element.id == this.selected)[0]
      this.isLoading = true
      this.$http.get("members-participation/get-all/" + this.selected)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
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
      this.isLoading = true
      this.$http.post("members-participation/update", this.form)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.getAllMembersParticipation()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.isLoading = false
            this.showSuccess = false
            this.error = error.response.data
            if(this.error.errors === undefined){
              this.showErrorInvalid = true
            }
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
      this.isLoading = true
      this.$http.post("members-participation/create", this.form2)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.getAllMembersParticipation()
            }else{
              this.showError = true
            }
          })
          .catch(error => {
            this.isLoading = false
            this.showSuccess = false
            this.error = error.response.data
            if(this.error.errors === undefined){
              this.showErrorInvalid = true
            }
            console.log(error)
            this.showError = true
          });

    },
    getPayedAmount(data) {
      var sum = 0;
      data.forEach(item => {
        sum += parseFloat(item.amount);
      });
      return parseFloat(sum)
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


      this.$bvModal.msgBoxConfirm('يجب عليك تأكيد عملية الحذف', {
        title: 'يرجى التأكيد',
        size: 'sm',
        buttonSize: 'sm',
        okVariant: 'danger',
        okTitle: 'نعم',
        cancelTitle: 'لا',
        footerClass: 'p-2',
        hideHeaderClose: false,
        centered: true
      })
          .then(value => {

            if(value === true) {


              this.isLoading = true
              this.$http.post("members-participation/delete", {id: idTransaction})
                  .then(response => {
                    this.isLoading = false
                    if(response.status === 200){
                      this.getAllMembersParticipation()
                    }
                  })
                  .catch(error => {
                    this.isLoading = false
                    this.error = error.response.data
                    if(this.error.errors === undefined){
                      this.showErrorInvalid = true
                    }
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