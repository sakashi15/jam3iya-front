<template>
  <div>
    <div v-if="hasAccess">
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
              <b-button variant="outline-info" v-b-modal.modal-center @click="hideAlerts(); resetAdd()">
                اضافة نفقة
              </b-button>
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
          v-if="items.length > 0"
          empty-text="لايوجد بيانات"
      >

        <template #cell(type)="data">
          <div class="d-flex align-items-center">
            <div class="ml-3">
              {{ (data.item.type != null) ? data.item.type.name : "-" }}
            </div>
          </div>
        </template>

        <template #cell(created_at)="data">
          <div class="d-flex align-items-center">
            <div class="ml-3">
              {{ (data.item.created_at != null) ? data.item.created_at.substr(0, 10).toString() : "" }}
            </div>
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
              تعديل / حذف
            </b-button>

          </div>
        </template>

      </b-table>

      <div v-if="items.length > 0">
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
          id="modal-center"
          hide-footer
          centered
          title="اضافة نفقة جديدة"
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
              <b-form @submit="onSubmit" @reset="onReset" v-if="show">
                <b-form-group id="input-group-3" label="النوع:" class="text-right">
                  <b-form-select
                      v-model="form.type"
                      :options="types"
                      required
                  ></b-form-select>
                </b-form-group>

                <b-form-group
                    id="input-group-1"
                    label="المبلغ:"
                    label-for="input-1"
                    class="text-right"
                >
                  <b-form-input
                      id="input-1"
                      v-model="form.amount"
                      type="number"
                      placeholder="ادخل المبلغ"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الوصف:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.description"
                      placeholder="ادخل الوصف"
                  ></b-form-input>
                </b-form-group>

                <div class="btn-grp">
                  <b-button type="submit" variant="primary">اضافة</b-button>
                  <b-button type="reset" variant="outline-danger">تفريغ</b-button>
                </div>
              </b-form>
            </b-col>
          </b-row>
          </b-overlay>
        </div>
      </b-modal>

      <b-modal
          id="modal-center2"
          hide-footer
          centered
          title="تعديل نفقة"
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
              <b-form @submit="onSubmit2" v-if="show2">
                <b-form-group id="input-group-3" label="النوع:" class="text-right">
                  <b-form-select
                      v-model="form.type"
                      :options="types"
                      required
                  ></b-form-select>
                </b-form-group>

                <b-form-group
                    id="input-group-1"
                    label="المبلغ:"
                    label-for="input-1"
                    class="text-right"
                >
                  <b-form-input
                      id="input-1"
                      v-model="form.amount"
                      type="number"
                      placeholder="ادخل المبلغ"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الوصف:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.description"
                      placeholder="ادخل الوصف"
                  ></b-form-input>
                </b-form-group>

                <div class="btn-grp">
                  <b-button type="submit" variant="primary">تعديل</b-button>
                  <b-button variant="outline-danger" @click="onReset2">حذف</b-button>
                </div>
              </b-form>
            </b-col>
          </b-row>
          </b-overlay>
        </div>
      </b-modal>

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
  name: "MonthTable3",
  data: () => ({
    title: "MonthTable3",
    types: [],
    filter: null,
    fields: [
      {
        key: "type",
        label: "النوع",
      },
      {
        key: "amount",
        label: "المبلغ (دج)",
      },
      {
        key: "description",
        label: "الوصف",
      },
      {
        key: "created_at",
        label: "التاريخ",
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
    pageOptions: [5, 10, 15, { value: 100, text: "اضهار اكثر" }],
    form: {
      amount : "",
      description : "",
      type : null
    },
    show: true,
    show2: true,
    error: null,
    showError: false,
    showSuccess: false,
    hasAccess: false,
    showErrorInvalid: false,
    isLoading: false
  }),
  mounted() {
    var role = localStorage.getItem("role");
    this.hasAccess = role === "admin"


    this.totalRows = this.items.length;
    this.getAllExpenses()
    this.getAllTypes()
  },
  methods: {
    getAllTypes() {
      this.isLoading = true
      this.$http.get("types/get-all")
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.types.push({
                value: null,
                text: "اختر نوع",
              })
              response.data.data.forEach(
                  (element) => this.types.push({
                    value: element.id,
                    text: element.name,
                  })
              );
            }else{
              this.types = []
              this.types.push({
                value: null,
                text: "اختر نوع",
              })
            }
            console.log(response.data.data)
          })
          .catch(error => {
            this.isLoading = false
            console.log(error.response.data)
          });
    },
    onSubmit(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false
      this.isLoading = true
      this.$http.post("expenses-dar-jma3a/create", this.form)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.resetAdd()
              this.getAllExpenses()
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
    getAllExpenses() {
      this.isLoading = true
      this.$http.get("expenses-dar-jma3a/get-all")
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
    onSubmit2(event) {
      event.preventDefault();

      this.showError = false
      this.showSuccess = false
      this.isLoading = true
      this.$http.post("expenses-dar-jma3a/update", this.form)
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.getAllExpenses()
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
    onReset(event) {
      event.preventDefault();
      this.form.amount = "";
      this.form.description = "";
      this.form.type = null;
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    },
    onReset2() {




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
              this.showError = false
              this.$http.post("expenses-dar-jma3a/delete", this.form)
                  .then(response => {
                    this.isLoading = false
                    if(response.status === 200){
                      this.show2 = false
                      this.showSuccess = true
                      this.getAllExpenses()
                    }else{
                      this.showError = true
                    }
                  })
                  .catch(error => {
                    this.isLoading = false
                    this.error = error.response.data
                    if(this.error.errors === undefined){
                      this.showErrorInvalid = true
                    }
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
      this.form.amount = item.amount;
      this.form.description = item.description;
      this.form.type = item.type.id;
    },
    resetAdd() {
      this.form.amount = "";
      this.form.description = "";
      this.form.type = null;
    },
    hideAlerts() {
      this.showError = false
      this.showSuccess = false
    }
  },
};
</script>