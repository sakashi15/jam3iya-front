<template>
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
              >تفريع</b-button
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
      <b-col md="4" cols="12" class="text-left">
        <div class="ml-auto mt-2 mt-md-0">
          <div class="btn-grp">
            <b-button variant="outline-info" v-b-modal.modal-center @click="hideAlerts(); resetAdd()">
              اضافة عضو
            </b-button>
          </div>
        </div>
      </b-col>
      <b-col md="4" cols="12" class="text-left">
        <div class="ml-auto mt-2 mt-md-0">
          <div class="btn-grp">
            <b-button variant="outline-info" v-b-modal.modal-center3 @click="hideAlerts(); currentFile = null">
              رفع ملف الاعضاء
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

      <template #cell(image)="data">
        <b-img
            :src="'https://mega-tech.dev/jam3iya/back-end/' + data.item.image"
            rounded="circle"
            width="50"
            height="50"
            v-if="data.item.image != null"
        />
<!--        <b-img-->
<!--            :src="'http://localhost:8000/' + data.item.image"-->
<!--            rounded="circle"-->
<!--            width="50"-->
<!--            height="50"-->
<!--            v-if="data.item.image != null"-->
<!--        />-->
        <b-img
            :src="require('@/assets/images/users/1.jpg')"
            rounded="circle"
            width="50"
            v-else
        />
      </template>
      <template #cell(full_name)="data">
        <div>
          <div>
            <h6 class="fw-medium mb-0">
              {{ data.item.full_name }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(email)="data">
        <div>
          <div>
            <h6 class="fw-medium mb-0">
              {{ data.item.email }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(phone)="data">
        <div>
          <div>
            <h6 class="fw-medium mb-0">
              {{ data.item.phone }}
            </h6>
          </div>
        </div>
      </template>
      <template #cell(birth_date)="data">
        <div>
          <div>
            <h6 class="fw-medium mb-0">
              {{ (data.item.birth_date != null) ? data.item.birth_date.toString() : "" }}
            </h6>
          </div>
        </div>
      </template>

      <template #cell(show_details)="data">
        <div>
          <b-button
            v-b-popover.hover.right="data.item.full_name"
            title="Nom complète"
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
        title="اضافة عضو جديد"
        class="text-right"
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
                <b-form-group
                    id="input-group-1"
                    label="عنوان البريد الالكتروني:"
                    label-for="input-1"
                    class="text-right"
                >
                  <b-form-input
                      id="input-1"
                      v-model="form.email"
                      type="email"
                      placeholder="ادخل البريد الالكتروني"
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الاسم واللقب:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.full_name"
                      placeholder="ادخل الاسم واللقب"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="تاريخ الميلاد:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      type="date"
                      v-model="form.birth_date"
                      placeholder="ادخل تاريخ الميلاد"
                      required
                      class="text-right"
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="رقم الهاتف:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.phone"
                      placeholder="ادخل رقم الهاتف"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الصورة:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-file
                      id="file-default"
                      type="file"
                      v-model="form.image"
                      placeholder="اختر صورة"
                      browse-text="اختر صورة"
                      class="text-left"
                  ></b-form-file>
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
        title="تعديل عضو"
        class="text-right"
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
                <b-form-group
                    id="input-group-1"
                    label="عنوان البريد الالكتروني:"
                    label-for="input-1"
                    class="text-right"
                >
                  <b-form-input
                      id="input-1"
                      v-model="form.email"
                      type="email"
                      placeholder="ادخل البريد الالكتروني"
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الاسم واللقب:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.full_name"
                      placeholder="ادخل الاسم واللقب"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="تاريخ الميلاد:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      type="date"
                      v-model="form.birth_date"
                      placeholder="ادخل تاريخ الميلاد"
                      required
                      class="text-right"
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="رقم الهاتف:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-input
                      id="input-2"
                      v-model="form.phone"
                      placeholder="ادخل رقم الهاتف"
                      required
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                    id="input-group-2"
                    label="الصورة:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-file
                      id="file-default"
                      class="text-left"
                      type="file"
                      v-model="currentImage"
                      placeholder="اختر صورة"
                      browse-text="اختر صورة"
                  ></b-form-file>
                </b-form-group>





                <div class="btn-grp">
                  <b-button type="submit" variant="primary">تعديل</b-button>
                  <b-button @click="onReset2" variant="outline-danger">حذف</b-button>
                </div>
              </b-form>
            </b-col>
          </b-row>
        </b-overlay>
      </div>
    </b-modal>

    <b-modal
        id="modal-center3"
        hide-footer
        centered
        title="رفع ملف اعضاء"
        class="text-right"
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
                <span class="mr-4 text-right" v-html="successMessage"></span>
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
              <b-form @submit="uploadFile" v-if="show3">
                <b-form-group
                    id="input-group-8"
                    label="الملف:"
                    label-for="input-2"
                    class="text-right"
                >
                  <b-form-file
                      id="file-default"
                      type="file"
                      v-model="currentFile"
                      accept=".xlsx, .xls, .csv"
                      placeholder="اختر ملف"
                      browse-text="اختر ملف"
                      class="text-left"
                  ></b-form-file>
                </b-form-group>
                <div class="btn-grp">
                  <b-button type="submit" variant="primary">استراد</b-button>
                </div>
              </b-form>
            </b-col>
          </b-row>
        </b-overlay>
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
        label: "الصورة",
      },
      {
        key: "full_name",
        label: "الاسم واللقب",
      },
      {
        key: "email",
        label: "الايميل",
      },
      {
        key: "phone",
        label: "الهاتف",
      },
      {
        key: "birth_date",
        label: "تاريخ الميلاد",
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
    pageOptions: [5, 10, 15, { value: 100, text: "استضهر اكثر" }],
    form: {
      email: "",
      full_name: "",
      birth_date: "",
      phone: "",
      image: null
    },
    show: true,
    show2: true,
    show3: true,
    error: null,
    showError: false,
    showSuccess: false,
    successMessage: "",
    showErrorInvalid: false,
    currentImage: null,
    currentFile: null,
    isLoading: false,
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
      this.isLoading = true
      this.$http.post("members/create", this.form, {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      })
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true
              this.getAllPersons()
              this.onReset()
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
    uploadFile(event) {
      event.preventDefault();
      if(this.currentFile != null){
        this.showError = false
        this.showSuccess = false
        this.isLoading = true
        this.$http.post("upload-members", {file: this.currentFile}, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
            .then(response => {
              this.isLoading = false
              if(response.status === 200){
                this.showSuccess = true
                this.successMessage = response.data.message
                this.getAllPersons()
                this.onReset3()
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
      }else{
        alert("يجب اختيار ملف اكسل")
      }
    },
    getAllPersons() {
      this.isLoading = true
      this.$http.get("members/get-all")
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
      if(this.currentImage !== null){
        this.form.image = this.currentImage;
      }else{
        delete this.form['image'];
      }
      this.currentImage = null
      this.isLoading = true
      this.$http.post("members/update", this.form, {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      })
          .then(response => {
            this.isLoading = false
            if(response.status === 200){
              this.showSuccess = true

              this.getAllPersons()
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
    onReset3() {
      this.currentFile = null;
      this.show3 = false;
      this.$nextTick(() => {
        this.show3 = true;
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



              this.showError = false
              this.currentImage = null
              this.isLoading = true
              this.$http.post("members/delete", this.form)
                  .then(response => {
                    this.isLoading = false
                    if(response.status === 200){
                      this.show2 = false
                      this.showSuccess = true
                      this.getAllPersons()
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