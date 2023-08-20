<template>
  <b-col cols="12">
    <b-card class="mb-4">
      <MonthTable></MonthTable>
    </b-card>
  </b-col>

</template>



<script>
import MonthTable from "../dashboards/dashboard-components/month-table/MonthTable4";

export default {
  name: 'MemberSubscriptions',
  data: () => ({

  }),
  components: {
    MonthTable,
  },

};
</script>
