<template>
  <div>

    <b-row>
<!--      <b-col cols="12" md="6" xl="6" class="d-flex align-items-stretch mb-8">-->
<!--        <CarouselAnimation />-->
<!--      </b-col>-->
      <b-col cols="12" class="d-flex align-items-stretch">
        <BaseCard title="Afaq Association - Zawya Lekbira Kerzaz" subtitle="Menu">
          <template v-slot:comcode>
            <div>
              <div class="d-flex flex-row">
                <div>
                  <img
                      src="@/assets/images/logo.jpeg"
                      alt="logo"
                      class="rounded-circle"
                      width="200"
                      height="100"
                  />
                </div>
                <div class="pl-0 mt-4">
                  <h3 class="mb-2">Afaq Association</h3>
                  <h6 class="mb-3">Zaouia Lekbira - Kerzaz</h6>
                </div>
              </div>
              <hr class="custom-hr" />
              <b-row>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img src="@/assets/images/users/people.png" class="img-fluid rounded-circle" width="65"/>
                    <h5 class="card-title mt-2 mb-1 pb-1">Gestion des Personnes</h5>

                    <b-button block href="/persons" pill variant="danger" class="mt-1 d-flex align-items-center justify-content-center">
                      Afficher
                    </b-button>
                  </b-card>
                </b-col>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img src="@/assets/images/users/money-management.png" class="img-fluid rounded-circle" width="65" />
                    <h5 class="card-title mt-2 mb-1 pb-1">Dépenses Ass. Afaq Zaouia</h5>

                    <b-button block href="/expenses" pill variant="primary" class="mt-1 d-flex align-items-center justify-content-center">
                      Afficher
                    </b-button>
                  </b-card>
                </b-col>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img src="@/assets/images/users/collateral.png" class="img-fluid rounded-circle" width="65" />
                    <h5 class="card-title mt-2 mb-1 pb-1">Dépenses Dar El Jama3a</h5>

                    <b-button block href="/expenses_dar_jma3a" pill variant="warning" class="mt-1 d-flex align-items-center justify-content-center text-white">
                      Afficher
                    </b-button>
                  </b-card>
                </b-col>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img
                        src="@/assets/images/users/payment.png"
                        class="img-fluid rounded-circle"
                        width="65"/>
                    <h5 class="card-title mt-2 mb-1 pb-1">Abonnement des Membres</h5>

                    <b-button
                        block
                        href="/member_subscriptions"
                        pill
                        variant="success"
                        class="mt-1 d-flex align-items-center justify-content-center">
                      Afficher</b-button>
                  </b-card>
                </b-col>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img
                        src="@/assets/images/users/statistics.png"
                        class="img-fluid rounded-circle"
                        width="65"/>
                    <h5 class="card-title mt-2 mb-1 pb-1">Afaq Association Statistiques</h5>

                    <b-button
                        block
                        href="/statistics"
                        pill
                        variant="info"
                        class="mt-1 d-flex align-items-center justify-content-center text-white">

                      Afficher</b-button>
                  </b-card>
                </b-col>
                <b-col cols="12" md="4" lg="2">
                  <b-card class="mb-1 text-center">
                    <img
                        src="@/assets/images/users/reminder.png"
                        class="img-fluid rounded-circle"
                        width="65"/>
                    <h5 class="card-title mt-2 mb-1 pb-1">Gestion des Années et Types</h5>

                    <b-button
                        block
                        href="/years-types"
                        pill
                        variant="primary"
                        class="mt-1 d-flex align-items-center justify-content-center">
                      Afficher
                    </b-button>
                  </b-card>
                </b-col>
              </b-row>
            </div>
          </template>
        </BaseCard>
      </b-col>
    </b-row>


  </div>
</template>

<script>
import BaseCard from '../../components/card/BaseCard';
export default {
  name: "Home",
  components:{
    //CarouselAnimation: () => import("@/components/ui/carousel/CarouselAnimation"),
    BaseCard
  },
  data: () => ({

  }),
}
</script>

<style scoped>

</style>