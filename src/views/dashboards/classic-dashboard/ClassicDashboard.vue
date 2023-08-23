<template>
  <div>
    <div v-if="hasAccess">
      <b-row>
        <!-- ---------------------------------------------------------
        Sales Summary
        --------------------------------------------------------- -->
        <b-col cols="12">
          <b-card class="mb-4" no-body>
            <SalesSummery></SalesSummery>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Email Campaigns
        --------------------------------------------------------- -->
        <b-col lg="8" cols="12" md="12">
          <b-card class="mb-4">
            <EmailCampaign></EmailCampaign>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Active Visitors
        --------------------------------------------------------- -->
        <b-col lg="4" cols="12" md="12">
          <b-card class="mb-4">
            <ActiveVisitors></ActiveVisitors>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Revenue Statistics
        --------------------------------------------------------- -->
        <b-col lg="4" cols="12" md="12" class="d-flex align-items-stretch">
          <b-card class="bg-primary mb-4 w-100">
            <RevenueStatics></RevenueStatics>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Page Views
        --------------------------------------------------------- -->
        <b-col lg="4" cols="12" md="12" class="d-flex align-items-stretch">
          <b-card no-body class="bg-info mb-4 w-100">
            <h4 class="card-title text-white p-3 mb-0">
              <span class="mb-2 d-block">Page Views</span>
              <i class="ti-arrow-up"></i> 6548
            </h4>
            <PageViews></PageViews>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Bounce Rate
        --------------------------------------------------------- -->
        <b-col lg="4" cols="12" md="12" class="d-flex align-items-stretch">
          <b-card class="mb-4 w-100">
            <h4 class="card-title mb-2 pb-1">Bounce Rate</h4>
            <BounceRate></BounceRate>
          </b-card>
        </b-col>
        <!-- ---------------------------------------------------------
        Projects of the Month
        --------------------------------------------------------- -->

      </b-row>
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
// -----------------------------------------
// Import Components Here
// -----------------------------------------
import SalesSummery from "../dashboard-components/sales-summery/SalesSummery";
import EmailCampaign from "../dashboard-components/email-campaign/EmailCampaign";
import ActiveVisitors from "../dashboard-components/active-visitors/ActiveVisitors";
import RevenueStatics from "../../widgets/chart-widgets/revenue-statics/RevenueStatics";
import PageViews from "../../widgets/chart-widgets/page-views/PageViews";
import BounceRate from "../../widgets/chart-widgets/bounce-rate/BounceRate";

// -----------------------------------------
// Export Here
// -----------------------------------------
export default {
  name: "ClassicDashboard",
  data: () => ({
    title: "ClassicDashboard",
    // Month Table
    month1: 0,
    monthoptions1: [
      { text: "Monthly", value: 0 },
      { text: "Daily", value: 1 },
      { text: "Weekly", value: 2 },
      { text: "Yearly", value: 3 },
    ],
    hasAccess: false
  }),
  components: {
    SalesSummery,
    EmailCampaign,
    ActiveVisitors,
    RevenueStatics,
    PageViews,
    BounceRate,
  },
  mounted() {
    var role = localStorage.getItem("role");
    this.hasAccess = role === "admin"
  }
};
</script>