<template>
  <b-row>
    <b-col>
      <b-card class="mb-4">
        <MonthTable></MonthTable>
      </b-card>
    </b-col>
    <b-col>
      <b-card class="mb-4">
        <MonthTable6></MonthTable6>
      </b-card>
    </b-col>
  </b-row>
</template>

<script>
import MonthTable from "@/views/dashboards/dashboard-components/month-table/MonthTable5";
import MonthTable6 from "@/views/dashboards/dashboard-components/month-table/MonthTable6";

export default {
  name: "YearsTypes",
  components: {
    MonthTable,
    MonthTable6,
  },
}
</script>

<style scoped>

</style>