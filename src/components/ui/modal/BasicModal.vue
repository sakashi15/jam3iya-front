<template>
  <!-- ----------------------------------------------------------------------------- -->
  <!-- Basic Modal -->
  <!-- ----------------------------------------------------------------------------- -->
  <BaseCard
    title="Basic Modal"
    subtitle="Modals are streamlined, but flexible dialog prompts powered by JavaScript and CSS."
    modalid="modal-1"
    modaltitle="Basic Modal"
  >
    <!-- --------------------------
    code view
    ---------------------------- -->

    <!-- --------------------------
    code view
    ---------------------------- -->

    <template v-slot:comcode>
      <div class="btn-grp">
        <b-button
          variant="outline-primary"
          id="show-btn"
          @click="$bvModal.show('modal-basic')"
          >Open Modal</b-button
        >
        <b-button variant="outline-info" v-b-modal.modal-center
          >Vertical Center</b-button
        >
        <b-button variant="outline-danger" v-b-modal.modal-no-backdrop
          >Hide Backdrop</b-button
        >
        <b-button variant="outline-warning" v-b-modal.modal-no-animation
          >No Animation</b-button
        >
      </div>
      <!-- ----------------------------------------- -->
      <!-- basic modal -->
      <!-- ----------------------------------------- -->

      <b-modal id="modal-basic" hide-footer>
        <template #modal-title> Basic Modal </template>
        <div class="d-block">
          <h6>Paragraph</h6>
          <p>
            Oat cake ice cream candy chocolate cake chocolate cake cotton candy
            dragée apple pie. Brownie carrot cake candy canes bonbon fruitcake
            topping halvah. Cake sweet roll cake cheesecake cookie chocolate
            cake liquorice.
          </p>
        </div>
        <div class="pt-3 border-top text-right">
          <b-button variant="primary" @click="$bvModal.hide('modal-basic')"
            >Accept</b-button
          >
        </div>
      </b-modal>
      <!-- ----------------------------------------- -->
      <!-- vertically center -->
      <!-- ----------------------------------------- -->

      <b-modal
        id="modal-center"
        hide-footer
        centered
        title="Vertically Center Modal"
      >
        <div class="d-block">
          <h6>Paragraph</h6>
          <p>
            Oat cake ice cream candy chocolate cake chocolate cake cotton candy
            dragée apple pie. Brownie carrot cake candy canes bonbon fruitcake
            topping halvah. Cake sweet roll cake cheesecake cookie chocolate
            cake liquorice.
          </p>
        </div>
        <div class="pt-3 border-top text-right">
          <b-button variant="info" @click="$bvModal.hide('modal-center')"
            >Accept</b-button
          >
        </div>
      </b-modal>

      <!-- ----------------------------------------- -->
      <!-- hide backdrop -->
      <!-- ----------------------------------------- -->
      <b-modal
        id="modal-no-backdrop"
        hide-backdrop
        hide-footer
        content-class="shadow"
        title="Hide Backdrop"
      >
        <div class="d-block">
          <h6>Paragraph</h6>
          <p>
            Oat cake ice cream candy chocolate cake chocolate cake cotton candy
            dragée apple pie. Brownie carrot cake candy canes bonbon fruitcake
            topping halvah. Cake sweet roll cake cheesecake cookie chocolate
            cake liquorice.
          </p>
        </div>
        <div class="pt-3 border-top text-right">
          <b-button variant="danger" @click="$bvModal.hide('modal-no-backdrop')"
            >Accept</b-button
          >
        </div>
      </b-modal>

      <!-- ----------------------------------------- -->
      <!-- no animation -->
      <!-- ----------------------------------------- -->
      <b-modal
        id="modal-no-animation"
        hide-backdrop
        hide-footer
        no-fade
        content-class="shadow"
        title="No Animation"
      >
        <div class="d-block">
          <h6>Paragraph</h6>
          <p>
            Oat cake ice cream candy chocolate cake chocolate cake cotton candy
            dragée apple pie. Brownie carrot cake candy canes bonbon fruitcake
            topping halvah. Cake sweet roll cake cheesecake cookie chocolate
            cake liquorice.
          </p>
        </div>
        <div class="pt-3 border-top text-right">
          <b-button
            variant="warning"
            @click="$bvModal.hide('modal-no-animation')"
            >Accept</b-button
          >
        </div>
      </b-modal>
    </template>
  </BaseCard>
</template>

<script>
import BaseCard from "../../card/BaseCard";

export default {
  name: "BasicModal",

  data: () => ({}),
  components: { BaseCard },
};
</script>